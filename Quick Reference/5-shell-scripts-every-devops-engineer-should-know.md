# 5 Shell Scripts Every DevOps Engineer Should Know

**Simple scripts. Big impact.** Automate · Monitor · Maintain · Be productive

This guide contains five practical Bash scripts for common DevOps tasks: disk space monitoring, log cleanup, service health checks, backups, and user management.

> **Safety note:** Review paths, permissions, retention periods, and commands before running these scripts on a real system. Test in a non-production environment first.

## Table of Contents

1. [Disk Space Monitoring Script](#1-disk-space-monitoring-script)
2. [Log File Cleanup Script](#2-log-file-cleanup-script)
3. [Service Health Check Script](#3-service-health-check-script)
4. [Automated Backup Script](#4-automated-backup-script)
5. [User Management Script](#5-user-management-script)

---

## 1. Disk Space Monitoring Script

Monitors root (`/`) filesystem usage and reports when it reaches the configured threshold.

```bash
#!/usr/bin/env bash

THRESHOLD=80

# Alert if disk usage is more than the threshold.
USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')

if [ "$USAGE" -ge "$THRESHOLD" ]; then
    echo "Disk usage is ${USAGE}%. Please check immediately!"
else
    echo "Disk usage is ${USAGE}%. All good."
fi
```

**What it does**
- Checks usage of the root filesystem.
- Prints a warning when usage is at or above `80%`.
- Can be scheduled with `cron`.

**Optional improvement:** Send an email, Slack notification, or monitoring alert when the threshold is exceeded.

---

## 2. Log File Cleanup Script

Deletes `.log` files older than the configured retention period in `/var/log/myapp`.

```bash
#!/usr/bin/env bash

LOG_DIR="/var/log/myapp"
DAYS=7

# Delete log files older than 7 days.
find "$LOG_DIR" -type f -name "*.log" -mtime +"$DAYS" -exec rm -f {} \;

echo "Old log files deleted successfully."
```

**What it does**
- Finds regular files ending in `.log`.
- Deletes files older than the configured number of days.
- Can be scheduled with `cron`.

**Safety recommendation:** First preview the files that would be deleted by running:

```bash
find /var/log/myapp -type f -name "*.log" -mtime +7 -print
```

Confirm the directory and retention policy before enabling deletion.

---

## 3. Service Health Check Script

Checks whether `nginx` is active and attempts to start it if it is not.

```bash
#!/usr/bin/env bash

SERVICE_NAME="nginx"

if systemctl is-active --quiet "$SERVICE_NAME"; then
    echo "$SERVICE_NAME is running."
else
    echo "$SERVICE_NAME is not running. Restarting..."
    sudo systemctl start "$SERVICE_NAME"
    echo "$SERVICE_NAME start command completed."
fi
```

**What it does**
- Checks service status with `systemctl`.
- Starts the service if it is not active.
- Can be extended to send alerts.

**Note:** The final message confirms that the start command completed, not necessarily that the service became healthy. For stronger validation, check `systemctl is-active --quiet "$SERVICE_NAME"` again after starting it.

---

## 4. Automated Backup Script

Creates a compressed `.tar.gz` archive of `/data` in `/backup`, using the current date in the filename.

```bash
#!/usr/bin/env bash

BACKUP_DIR="/backup"
SOURCE_DIR="/data"
DATE=$(date +%F)

mkdir -p "$BACKUP_DIR"

tar -czf "$BACKUP_DIR/backup_${DATE}.tar.gz" "$SOURCE_DIR"

echo "Backup completed: $BACKUP_DIR/backup_${DATE}.tar.gz"
```

**What it does**
- Creates the backup directory if it does not exist.
- Compresses the source directory into a dated archive.
- Can be scheduled with `cron`.

**Important:** This basic example does not verify archive integrity, retention, restore procedures, or off-host storage. Test restoration regularly and consider adding error handling before using it for important data.

---

## 5. User Management Script

Creates a user, sets a password from standard input, and adds the user to the `sudo` group.

```bash
#!/usr/bin/env bash

USERNAME="${1:-}"

if [ -z "$USERNAME" ]; then
    echo "Usage: $0 <username>"
    exit 1
fi

sudo useradd -m "$USERNAME"

# Set a password interactively instead of hardcoding one.
sudo passwd "$USERNAME"

sudo usermod -aG sudo "$USERNAME"

echo "User $USERNAME created and added to the sudo group."
```

**Usage**

```bash
chmod +x create_user.sh
./create_user.sh harish
```

**What it does**
- Requires a username argument.
- Creates the user with a home directory.
- Prompts an administrator to set the password.
- Adds the user to the `sudo` group.

**Important:** Adding a user to `sudo` grants administrative privileges. Only do this for authorized users and follow your organization's access-control policy. The original image shows a hardcoded example password; this version avoids embedding a password in a script.

---

## Quick Reference

| Script | Purpose | Key command |
|---|---|---|
| Disk space monitoring | Warn when root filesystem usage reaches a threshold | `df`, `awk` |
| Log cleanup | Delete old application logs | `find` |
| Service health check | Check and start a service | `systemctl` |
| Automated backup | Create a compressed dated archive | `tar` |
| User management | Create a user and grant sudo access | `useradd`, `passwd`, `usermod` |

## Scheduling with Cron

Use `crontab -e` to edit a user's cron table. For example, to run a script every day at 2:00 AM:

```cron
0 2 * * * /path/to/script.sh
```

Use an absolute script path, configure logging, and ensure the cron user has the required permissions. Run privileged operations only with appropriate authorization.

## Before Running in Production

- [ ] Test each script in a safe environment.
- [ ] Verify paths, permissions, and retention settings.
- [ ] Add logging and meaningful exit codes.
- [ ] Configure monitoring or notifications where required.
- [ ] Validate backups by restoring them.
- [ ] Review access grants before adding users to `sudo`.
