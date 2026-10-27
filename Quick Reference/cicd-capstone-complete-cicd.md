# Day 176 — Complete CI/CD for the Capstone

**EngiDock DevOps Training | Month 6: Terraform, Monitoring & Portfolio**

> This Markdown version is converted from the supplied 21-page PDF. It preserves the source's structure and technical content; PDF code blocks and diagrams may need visual comparison with the original before production use.


---

<!-- Source PDF page 1 -->

EngiDock DevOps Training | Month 6: Terraform, Monitoring & Portfolio
Topic: Yesterday you wrote the Terraform that creates the cloud. Today you write the GitHub Actions
workflows that ship code into that cloud. Three pipelines: a PR pipeline that lints and tests on every pull
request, a main pipeline that builds the image, pushes to ACR, plans the Terraform, and deploys to staging on
every merge to main, and a release pipeline that applies the Terraform for prod and deploys to prod with a
manual approval gate on every git tag. By the end of today, git tag v1.0.0 && git push --tags triggers a
fully automated, approval-gated deployment to production.
A capstone repo with no CI/CD is a repo of scripts. A senior engineer clones it, runs the app, looks around, and
concludes "this person can write code but they have never shipped to anyone." The CI/CD pipeline is the
artifact that proves you have shipped — because shipping means automating the path from commit to
running system, not running docker compose up on a server you SSH into. The three-pipeline structure (PR,
main, release) is the production pattern used by every serious software company, and reproducing it in your
capstone is the difference between a portfolio project and a junior DevOps engineer's body of work.
The reason three pipelines instead of one is separation of concerns. The PR pipeline runs fast (under 2
minutes) and answers one question: "is this commit safe to merge?" It runs lint and unit tests on every push to
a PR branch. The main pipeline runs on merges to main and answers a different question: "is this commit safe
for staging?" It builds the image, pushes to ACR, runs terraform plan (not apply — staging infra already
exists), and deploys to staging. The release pipeline runs on git tags and answers the most expensive question:
"is this commit safe for prod?" It runs terraform apply (creating prod infra if needed), deploys to prod, but
only after a human clicks Approve in the GitHub UI. Each pipeline has a different trigger, a different runtime
cost, and a different blast radius. Conflating them into one mega-pipeline is the most common CI/CD mistake.
The second reason this matters is approval gates. A release pipeline that deploys to prod without an approval
gate is not a release pipeline — it is a deployment bomb. The first time a bad commit gets tagged, prod goes
down and there is no human in the loop. GitHub Environments provide the approval gate natively: you create a
production environment, configure it to require one reviewer, and any job that targets that environment
blocks until the reviewer approves. This is a 10-line YAML change that prevents the most expensive class of
DevOps mistake. Showing this in the capstone demonstrates you understand production discipline, not just
pipeline mechanics.
The third reason is OIDC authentication. The old way to authenticate GitHub Actions to Azure was to create a
service principal, store its client secret in a GitHub Actions secret, and rotate that secret every 6 months.
Everyone forgot to rotate. Secrets leaked. The new way is OIDC: GitHub Actions presents a short-lived OIDC
token to Azure, Azure validates it against a federated credential configuration, and Azure issues an access
token valid for one hour. No stored secrets. No rotation. The capstone uses OIDC because that is what
production code in 2025 looks like. A reviewer who sees azure/login@v2 with client-id, tenant-id, and
subscription-id but no client-secret knows you are current. A reviewer who sees a stored
AZURE_CLIENT_SECRET knows you are not.

## Day 176 — Complete CI/CD for the Capstone


## Why Complete CI/CD Is Essential in DevOps


---

<!-- Source PDF page 2 -->

The fourth reason is the deployment mechanism. The naive pattern is "SSH into the VM and docker compose
pull." The production pattern is "use a deployment script that pulls, waits for health, and rolls back if health
fails." The capstone uses a deployment script that runs on the VM via SSH, pulls the new image, restarts the
app container, polls /health/ready for up to 60 seconds, and if health fails, restarts the previous image and
exits non-zero so the workflow fails. This is a 30-line bash script that demonstrates you understand
deployment safety. Tomorrow's monitoring day builds on this — if the deploy fails, Prometheus alerts fire; if it
succeeds, the dashboard shows the new version.
Finally, today's pipeline is what makes the capstone "live." Once the three pipelines are green, you can push a
code change and watch it reach staging in 5 minutes, then tag it and watch it reach prod in 10 minutes
(including the approval gate). That end-to-end loop is what you will demonstrate in the interview. The
interviewer asks "show me your CI/CD" — you open the Actions tab, point at the three pipelines, push a one-
line code change, and watch the PR pipeline go green, the main pipeline deploy to staging, and then tag it and
watch the release pipeline deploy to prod. That demo is the interview. The pipeline is the demo. Build it today.
The deploy step is identical between staging and prod — the only differences are the VM IP, the image tag, and
the ACR login server. A composite action lets you write that step once and call it twice with different inputs.
Without the composite action, you copy-paste 40 lines of YAML between main.yml and release.yml, and the
next time you fix a bug in the deploy logic, you forget to fix it in one of the two copies. The composite action is
the DRY principle applied to CI/CD. An interviewer who sees .github/actions/ knows you have felt the copy-
paste pain and learned to factor it out.
Before writing any workflow YAML, configure the environments and secrets in the GitHub UI (or CLI).
Environments are the mechanism for approval gates and environment-scoped secrets.

## Section 1 — Repository Layout for CI/CD


## Why a Composite Action


## Section 2 — GitHub Environments and Secrets

capstone/
├── .github/
│   ├── workflows/
│   │   ├── pr.yml              # PR pipeline: lint + test on PR
│   │   ├── main.yml            # Main pipeline: build + push + plan + stage deploy
│   │   └── release.yml         # Release pipeline: apply + prod deploy w/ approval
│   └── actions/
│       └── deploy-to-vm/       # Composite action for SSH-based deploy
│           └── action.yml
├── scripts/
│   ├── deploy.sh               # Runs on the VM: pull, restart, health check, rollback
│   └── healthcheck.sh          # Polls /health/ready with timeout
├── app/
├── infra/
└── ...

---

<!-- Source PDF page 3 -->


## Creating Environments


## Storing Secrets (Non-Sensitive Ones — OIDC Means No Azure Secret)


## The OIDC Federated Credential in Azure

# From your repo directory
gh api repos/:owner/:repo/environments/staging -X PUT
gh api repos/:owner/:repo/environments/production -X PUT
# Configure production to require 1 approval
gh api repos/:owner/:repo/environments/production \
-X PATCH \
-f required_reviewers[0]="$GITHUB_USERNAME"
# Or via UI: Settings > Environments > production > Required reviewers
# These are NOT secret — they are public identifiers. But Actions needs them as vars.
gh variable set AZURE_TENANT_ID       --body "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa"
gh variable set AZURE_SUBSCRIPTION_ID --body "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
gh variable set AZURE_CLIENT_ID       --body "cccccccc-cccc-cccc-cccc-cccccccccccc"
# Note: no AZURE_CLIENT_SECRET. OIDC means we do not need one.
# These ARE sensitive — store as secrets, scoped per environment.
gh secret set TFSTATE_STORAGE_ACCOUNT --body "sttfstate1234"
gh secret set VM_SSH_PRIVATE_KEY      --env staging   --body "$(cat
~/.ssh/capstone_staging)"
gh secret set VM_SSH_PRIVATE_KEY      --env production --body "$(cat ~/.ssh/capstone_prod)"
gh secret set SLACK_WEBHOOK_URL       --body "https://hooks.slack.com/services/..."
# One-time setup: tell Azure to trust your GitHub repo
APP_ID=$(az ad app create --display-name "github-capstone-oidc" \
--query appId -o tsv)
# Create the federated credential
cat > /tmp/fed_cred.json <<EOF
{
"name": "github-capstone-main",
"issuer": "https://token.actions.githubusercontent.com",
"subject": "repo:yourusername/capstone:ref:refs/heads/main",
"audiences": ["api://AzureADTokenExchange"]
}
EOF
az ad app federated-credential create --id $APP_ID --parameters /tmp/fed_cred.json
# Assign Contributor role on the resource group
az role assignment create \
--assignee $APP_ID \
--role "Contributor" \

---

<!-- Source PDF page 4 -->

The subject field is what Azure checks against the OIDC token GitHub presents. The format
repo:yourusername/capstone:ref:refs/heads/main means "this credential is valid only for the main branch
of this specific repo." If an attacker forks your repo, their fork's OIDC token has a different subject and Azure
rejects it. This is the security model of OIDC: scope credentials to specific refs, not to repos in general.

## The Subject Claim


## Section 3 — The PR Pipeline


### `.github/workflows/pr.yml`

--scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/rg-tasks-staging"
# Repeat for rg-tasks-prod
name: PR Check

### on


### pull_request

branches: [main]

### permissions

contents: read

### concurrency

group: pr-${{ github.event.pull_request.number }}
cancel-in-progress: true

### jobs


### lint

name: Lint
runs-on: ubuntu-latest

### steps

- uses: actions/checkout@v4
- name: Set up Python
uses: actions/setup-python@v5

### with

python-version: "3.11"
cache: pip
- name: Install dependencies
run: |
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install ruff mypy pytest pytest-cov
- name: Ruff check
run: ruff check app/

---

<!-- Source PDF page 5 -->

- name: Ruff format check
run: ruff format --check app/
- name: Mypy
run: mypy app/ --ignore-missing-imports

### test

name: Test
runs-on: ubuntu-latest

### services


### postgres

image: postgres:15

### env

POSTGRES_USER: tasks
POSTGRES_PASSWORD: tasks
POSTGRES_DB: tasks_test
ports: ["5432:5432"]
options: >-
--health-cmd pg_isready
--health-interval 10s
--health-timeout 5s
--health-retries 5

### redis

image: redis:7
ports: ["6379:6379"]
options: >-
--health-cmd "redis-cli ping"
--health-interval 10s
--health-timeout 5s
--health-retries 5

### env

DATABASE_URL: postgres://tasks:tasks@localhost:5432/tasks_test
REDIS_URL: redis://localhost:6379/1
FLASK_SECRET: test-secret
ENVIRONMENT: local

### steps

- uses: actions/checkout@v4
- name: Set up Python
uses: actions/setup-python@v5

### with

python-version: "3.11"
cache: pip
- name: Install dependencies
run: |
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install pytest pytest-cov
- name: Run migrations

---

<!-- Source PDF page 6 -->

The PR pipeline runs on every push to a PR branch, which can be dozens of times per PR. It must be fast. Three
optimizations: (1) cache: pip reuses the pip download cache across runs, saving 30 seconds of pip install.
(2) concurrency cancels the previous run when you push a new commit, so you do not pay for stale runs. (3)
terraform init -backend=false skips remote state, which is unnecessary for validate and saves 5-10
seconds of network I/O. The three jobs run in parallel — lint, test, and terraform-validate are
independent and finish at roughly the same time.
In the GitHub repo settings, under Branches > Branch protection for main, mark Lint, Test, and Terraform
Validate as required status checks. Once configured, no PR can merge until all three pass. This is the gate
that prevents bad code from reaching main. Without it, the PR pipeline is a suggestion, not a gate.

## Why This Pipeline Runs in Under 2 Minutes


## The Required Status Check

run: flask --app app db upgrade || true
- name: Run tests
run: pytest --cov=app --cov-report=xml --cov-report=term-missing
- name: Upload coverage
if: always()
uses: actions/upload-artifact@v4

### with

name: coverage-${{ github.event.pull_request.number }}
path: coverage.xml

### terraform-validate

name: Terraform Validate
runs-on: ubuntu-latest

### steps

- uses: actions/checkout@v4
- name: Set up Terraform
uses: hashicorp/setup-terraform@v3

### with

terraform_version: "1.5.7"
- name: Terraform fmt
working-directory: infra
run: terraform fmt -check -recursive
- name: Terraform init
working-directory: infra
run: terraform init -backend=false
- name: Terraform validate
working-directory: infra
run: terraform validate

---

<!-- Source PDF page 7 -->


## Section 4 — The Main Pipeline (Build + Plan + Staging

Deploy)


### `.github/workflows/main.yml`

name: Main Pipeline

### on


### push

branches: [main]

### permissions

contents: read
id-token: write       # required for OIDC
packages: write

### concurrency

group: main-pipeline
cancel-in-progress: false  # never cancel a deploy in progress

### jobs


### test

name: Test
runs-on: ubuntu-latest

### services


### postgres

image: postgres:15
env: { POSTGRES_USER: tasks, POSTGRES_PASSWORD: tasks, POSTGRES_DB: tasks_test }
ports: ["5432:5432"]
options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-
retries 5

### redis

image: redis:7
ports: ["6379:6379"]
options: --health-cmd "redis-cli ping" --health-interval 10s --health-timeout 5s --
health-retries 5

### env

DATABASE_URL: postgres://tasks:tasks@localhost:5432/tasks_test
REDIS_URL: redis://localhost:6379/1
FLASK_SECRET: test-secret
ENVIRONMENT: local

### steps

- uses: actions/checkout@v4
- uses: actions/setup-python@v5
with: { python-version: "3.11", cache: pip }
- run: pip install -r requirements.txt pytest pytest-cov
- run: pytest --cov=app --cov-report=term-missing

### build-and-push

name: Build and Push Image
needs: test
runs-on: ubuntu-latest

---

<!-- Source PDF page 8 -->


### outputs

image_tag: ${{ steps.meta.outputs.version }}

### steps

- uses: actions/checkout@v4
- name: Azure login (OIDC)
uses: azure/login@v2

### with

client-id: ${{ vars.AZURE_CLIENT_ID }}
tenant-id: ${{ vars.AZURE_TENANT_ID }}
subscription-id: ${{ vars.AZURE_SUBSCRIPTION_ID }}
- name: ACR login
run: az acr login --name ${{ vars.ACR_NAME }}
- name: Docker metadata
id: meta
uses: docker/metadata-action@v5

### with

images: ${{ vars.ACR_LOGIN_SERVER }}/tasks
tags: |
type=sha,format=long
type=raw,value=latest
type=raw,value=main-${{ github.run_number }}
- name: Build and push
uses: docker/build-push-action@v5

### with

context: .
file: ./Dockerfile
push: true
tags: ${{ steps.meta.outputs.tags }}
labels: ${{ steps.meta.outputs.labels }}
cache-from: type=registry,ref=${{ vars.ACR_LOGIN_SERVER }}/tasks:latest
cache-to: type=inline

### terraform-plan

name: Terraform Plan (Staging)
needs: build-and-push
runs-on: ubuntu-latest

### defaults


### run

working-directory: infra

### steps

- uses: actions/checkout@v4
- uses: azure/login@v2

### with

client-id: ${{ vars.AZURE_CLIENT_ID }}
tenant-id: ${{ vars.AZURE_TENANT_ID }}
subscription-id: ${{ vars.AZURE_SUBSCRIPTION_ID }}
- uses: hashicorp/setup-terraform@v3

---

<!-- Source PDF page 9 -->

The PR pipeline cancels stale runs because a PR push invalidates the previous run. The main pipeline does not
cancel because a deploy in progress must complete — cancelling mid-deploy leaves the VM in an inconsistent
state (new image pulled, old container killed, new container not yet started). This is the kind of detail that an
interviewer probes: "what happens if you push two commits to main in quick succession?" The answer is "the
second run queues behind the first because of the concurrency group, and cancel-in-progress: false
ensures the first finishes."

## Why cancel-in-progress: false

with: { terraform_version: "1.5.7" }
- name: Terraform init
run: terraform init

### env

ARM_STORAGE_ACCOUNT_NAME: ${{ secrets.TFSTATE_STORAGE_ACCOUNT }}
- name: Terraform plan
run: terraform plan -var-file=tfvars/staging.tfvars -no-color

### env

ARM_STORAGE_ACCOUNT_NAME: ${{ secrets.TFSTATE_STORAGE_ACCOUNT }}

### deploy-staging

name: Deploy to Staging
needs: terraform-plan
runs-on: ubuntu-latest

### environment

name: staging
url: http://${{ vars.STAGING_VM_IP }}

### steps

- uses: actions/checkout@v4
- name: Deploy to VM
uses: ./.github/actions/deploy-to-vm

### with

vm_ip: ${{ vars.STAGING_VM_IP }}
ssh_key: ${{ secrets.VM_SSH_PRIVATE_KEY }}
acr_login_server: ${{ vars.ACR_LOGIN_SERVER }}
image_tag: ${{ needs.build-and-push.outputs.image_tag }}
health_check_url: http://localhost/health/ready
environment: staging
- name: Notify Slack
if: always()
uses: slackapi/slack-github-action@v1.24.0

### with

payload: |
{ "text": "Staging deploy ${{ job.status }}: commit ${{ github.sha }} by ${{
github.actor }}" }

### env

SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

---

<!-- Source PDF page 10 -->

Staging infrastructure already exists from Day 175's manual apply. The main pipeline runs terraform plan to
detect drift — if someone manually changed a NSG rule, the plan will show it. If the plan shows changes, you
investigate before the next deploy. Running terraform apply on every merge to main is a valid pattern
(continuous infrastructure) but it adds risk: a typo in a tfvars file can delete a resource. For the capstone, plan-
only on main and apply-only on release is the safer pattern. The interview answer is "I run plan on main to
detect drift, and apply only on tagged releases so infrastructure changes go through the same approval gate as
code changes."

## Why terraform plan and Not terraform apply in the Main

Pipeline


## Section 5 — The Composite Action for Deploy


### `.github/actions/deploy-to-vm/action.yml`

name: Deploy to VM
description: SSH into a VM, pull the latest image, restart the app, and verify health.

### inputs


### vm_ip

description: Public IP of the target VM
required: true

### ssh_key

description: SSH private key for the VM
required: true

### acr_login_server

description: ACR login server (e.g. acrtasksstaging.azurecr.io)
required: true

### image_tag

description: Image tag to deploy
required: true

### health_check_url

description: URL to poll for health (e.g. http://localhost/health/ready)
required: true

### environment

description: Environment name (staging or production)
required: true

### runs

using: composite

### steps

- name: Write SSH key
run: |
mkdir -p ~/.ssh
echo "${{ inputs.ssh_key }}" > ~/.ssh/id_rsa
chmod 600 ~/.ssh/id_rsa
ssh-keyscan -H ${{ inputs.vm_ip }} >> ~/.ssh/known_hosts
shell: bash
- name: Copy deploy script

---

<!-- Source PDF page 11 -->


### `scripts/deploy.sh`

run: scp scripts/deploy.sh azureadmin@${{ inputs.vm_ip }}:/tmp/deploy.sh
shell: bash
- name: Run deploy
run: |
ssh azureadmin@${{ inputs.vm_ip }} \
"chmod +x /tmp/deploy.sh && \
/tmp/deploy.sh \
--acr-login-server ${{ inputs.acr_login_server }} \
--image-tag ${{ inputs.image_tag }} \
--health-check-url '${{ inputs.health_check_url }}' \
--environment ${{ inputs.environment }}"
shell: bash
#!/usr/bin/env bash
# scripts/deploy.sh — runs on the VM during deploy
# Pulls new image, restarts app, waits for health, rolls back on failure.
set -euo pipefail
ACR_LOGIN_SERVER=""
IMAGE_TAG=""
HEALTH_CHECK_URL=""
ENVIRONMENT=""
while [[ $# -gt 0 ]]; do
case $1 in
--acr-login-server) ACR_LOGIN_SERVER="$2"; shift 2 ;;
--image-tag)        IMAGE_TAG="$2"; shift 2 ;;
--health-check-url) HEALTH_CHECK_URL="$2"; shift 2 ;;
--environment)      ENVIRONMENT="$2"; shift 2 ;;
*) echo "Unknown arg: $1"; exit 1 ;;
esac
done
APP_DIR="/opt/app"
IMAGE="${ACR_LOGIN_SERVER}/tasks:${IMAGE_TAG}"
PREV_IMAGE_FILE="${APP_DIR}/.previous_image"
echo "[$(date -u)] Deploying ${IMAGE} to ${ENVIRONMENT}"
cd "$APP_DIR"
# Save current image for rollback
CURRENT_IMAGE=$(docker compose config --images 2>/dev/null | grep
"^${ACR_LOGIN_SERVER}/tasks" | head -1 || echo "")
if [[ -n "$CURRENT_IMAGE" ]]; then
echo "$CURRENT_IMAGE" > "$PREV_IMAGE_FILE"
fi

---

<!-- Source PDF page 12 -->

If the deploy script returns 0 even on rollback, the GitHub Actions job shows green and you do not know the
deploy failed. By returning non-zero, the job fails, the Slack notification fires, and you are alerted. This is the
difference between "the deploy happened" and "the deploy succeeded." The job status reflects deploy success,
not script execution.

## Why the Deploy Script Returns Non-Zero on Failure

# Login to ACR using VM's managed identity
az acr login --name "$ACR_LOGIN_SERVER"
# Update docker-compose.yml with new image tag
sed -i "s|image: ${ACR_LOGIN_SERVER}/tasks:.*|image: ${IMAGE}|" docker-compose.yml
# Pull the new image
docker compose pull app
# Restart only the app container (prometheus, grafana stay up)
docker compose up -d --no-deps app
# Wait for health
echo "[$(date -u)] Waiting for health check at ${HEALTH_CHECK_URL}..."
for i in $(seq 1 60); do
HTTP_CODE=$(curl -s -o /dev/null -w "%{http_code}" "$HEALTH_CHECK_URL" || echo "000")
if [[ "$HTTP_CODE" == "200" ]]; then
echo "[$(date -u)] Health check passed after ${i}s"
echo "[$(date -u)] Deploy of ${IMAGE} to ${ENVIRONMENT} SUCCEEDED"
exit 0
fi
sleep 1
done
# Health check failed — roll back
echo "[$(date -u)] Health check FAILED after 60s. Rolling back."
if [[ -f "$PREV_IMAGE_FILE" ]]; then
PREV_IMAGE=$(cat "$PREV_IMAGE_FILE")
echo "[$(date -u)] Rolling back to ${PREV_IMAGE}"
sed -i "s|image: ${ACR_LOGIN_SERVER}/tasks:.*|image: ${PREV_IMAGE}|" docker-compose.yml
docker compose pull app
docker compose up -d --no-deps app
sleep 5
HTTP_CODE=$(curl -s -o /dev/null -w "%{http_code}" "$HEALTH_CHECK_URL" || echo "000")
if [[ "$HTTP_CODE" == "200" ]]; then
echo "[$(date -u)] Rollback succeeded. Previous version is running."
else
echo "[$(date -u)] ROLLBACK ALSO FAILED. Manual intervention required."
fi
fi
echo "[$(date -u)] Deploy of ${IMAGE} to ${ENVIRONMENT} FAILED"
exit 1

---

<!-- Source PDF page 13 -->


## Section 6 — The Release Pipeline (Apply + Prod Deploy with

Approval)


### `.github/workflows/release.yml`

name: Release Pipeline

### on


### push

tags: ["v*"]

### permissions

contents: read
id-token: write
packages: write

### concurrency

group: release-pipeline
cancel-in-progress: false

### jobs


### test

name: Test
runs-on: ubuntu-latest
# ... identical to main.yml test job

### build-and-push

name: Build and Push Image (Tagged)
needs: test
runs-on: ubuntu-latest

### outputs

image_tag: ${{ steps.meta.outputs.version }}

### steps

- uses: actions/checkout@v4
- uses: azure/login@v2

### with

client-id: ${{ vars.AZURE_CLIENT_ID }}
tenant-id: ${{ vars.AZURE_TENANT_ID }}
subscription-id: ${{ vars.AZURE_SUBSCRIPTION_ID }}
- run: az acr login --name ${{ vars.ACR_NAME }}
- name: Docker metadata
id: meta
uses: docker/metadata-action@v5

### with

images: ${{ vars.ACR_LOGIN_SERVER }}/tasks
tags: |
type=semver,pattern={{version}}
type=semver,pattern={{major}}.{{minor}}
type=raw,value=prod
- name: Build and push
uses: docker/build-push-action@v5

### with


---

<!-- Source PDF page 14 -->

context: .
push: true
tags: ${{ steps.meta.outputs.tags }}
cache-from: type=registry,ref=${{ vars.ACR_LOGIN_SERVER }}/tasks:latest

### terraform-apply-prod

name: Terraform Apply (Prod)
needs: build-and-push
runs-on: ubuntu-latest
environment: production   # this triggers the approval gate

### defaults


### run

working-directory: infra

### steps

- uses: actions/checkout@v4
- uses: azure/login@v2

### with

client-id: ${{ vars.AZURE_CLIENT_ID }}
tenant-id: ${{ vars.AZURE_TENANT_ID }}
subscription-id: ${{ vars.AZURE_SUBSCRIPTION_ID }}
- uses: hashicorp/setup-terraform@v3
with: { terraform_version: "1.5.7" }
- name: Terraform init
run: terraform init

### env

ARM_STORAGE_ACCOUNT_NAME: ${{ secrets.TFSTATE_STORAGE_ACCOUNT }}
- name: Terraform apply
run: terraform apply -var-file=tfvars/prod.tfvars -auto-approve

### env

ARM_STORAGE_ACCOUNT_NAME: ${{ secrets.TFSTATE_STORAGE_ACCOUNT }}

### deploy-prod

name: Deploy to Prod
needs: terraform-apply-prod
runs-on: ubuntu-latest
environment: production   # also gated

### steps

- uses: actions/checkout@v4
- name: Deploy to VM
uses: ./.github/actions/deploy-to-vm

### with

vm_ip: ${{ vars.PROD_VM_IP }}
ssh_key: ${{ secrets.VM_SSH_PRIVATE_KEY }}
acr_login_server: ${{ vars.ACR_LOGIN_SERVER }}
image_tag: ${{ needs.build-and-push.outputs.image_tag }}
health_check_url: http://localhost/health/ready
environment: production
- name: Notify Slack
if: always()
uses: slackapi/slack-github-action@v1.24.0

### with

payload: |

---

<!-- Source PDF page 15 -->

terraform-apply-prod and deploy-prod are separate jobs both targeting the production environment.
The first time the workflow runs, GitHub will pause at terraform-apply-prod and request approval. Once
approved, it runs, then deploy-prod requests approval again. This double-gate is intentional: applying
Terraform to prod is one risky action, deploying a new image to prod is another. An approver might be
comfortable with the Terraform change but not with the code change, or vice versa. Two gates means two
chances to abort. The interview answer is "I separate apply and deploy into two approval-gated jobs because
they have different risk profiles and I want a human review at each."
This is the diagram that goes in your README under "CI/CD Flow."

## Why Two Jobs Both Reference environment: production


## The Approval Workflow in Practice

1. You tag v1.0.0 and push the tag.
2. The release pipeline runs test, then build, then waits at terraform-apply-prod.
3. GitHub emails the configured reviewer: "Approval required for production."
4. The reviewer opens the Actions tab, reviews the diff, clicks "Approve."
5. terraform-apply-prod runs (5 minutes), then deploy-prod waits for approval.
6. Reviewer approves again.
7. deploy-prod runs (1 minute), Slack fires "PROD deploy success: v1.0.0."
8. The whole loop takes ~15 minutes end-to-end including approval latency.

## Section 7 — The Full CI/CD Flow as a Diagram

{ "text": "PROD deploy ${{ job.status }}: tag ${{ github.ref_name }} by ${{
github.actor }}" }

### env

SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
┌──────────────┐
│  Developer   │
│  writes code │
└──────┬───────┘
│ git push origin feature-branch
▼
┌──────────────┐
│  Open PR     │  ── ▶  PR Pipeline (lint + test + tf validate)
│  to main     │      ◀ ── blocks merge on failure
└──────┬───────┘
│ merge to main (after PR pipeline green)
▼
┌──────────────┐
│  Main        │  ── ▶  Test job
│  Pipeline    │  ── ▶  Build & push image (ACR, tags: sha, latest, main-N)
│  (on push    │  ── ▶  terraform plan (staging) — drift detection only
│   to main)   │  ── ▶  deploy to staging (composite action + deploy.sh)

---

<!-- Source PDF page 16 -->

Failure Where It Catches Result
Bad code (lint fail, test fail) PR pipeline PR cannot merge
Bad infra (terraform plan shows
destruction) Main pipeline plan stepStaging deploy skipped, alert fires
Bad deploy (health check fails)deploy.sh in composite
action
Rollback to previous image, job fails,
Slack alerts
Every job that matters (deploy-staging, deploy-prod, terraform-apply-prod) ends with a Slack notification step
that runs if: always(). The always() is critical — without it, the step only runs on success and failures stay
silent. The payload includes: job status, commit SHA, actor (who triggered), and a link to the workflow run.

## The Three Failure Modes


## Section 8 — Slack Notifications and Failure Handling


## The Slack Payload Pattern

└──────┬───────┘      ◀ ── Slack notification on success/failure
│ manual verification in staging
│ git tag v1.0.0 && git push --tags
▼
┌──────────────┐
│  Release     │  ── ▶  Test job
│  Pipeline    │  ── ▶  Build & push image (tags: v1.0.0, 1.0, prod)
│  (on tag)    │  ── ▶  terraform apply (prod) — WAITING FOR APPROVAL
└──────┬───────┘  ── ▶  deploy to prod — WAITING FOR APPROVAL
│ human clicks "Approve" twice (once for apply, once for deploy)
▼
┌──────────────┐
│  Production  │
│  running     │
│  v1.0.0      │
└──────────────┘
- name: Notify Slack
if: always()
uses: slackapi/slack-github-action@v1.24.0

### with

payload: |
{
"text": "${{ job.status == 'success' && ':white_check_mark:' || ':x:' }} ${{
github.workflow }}: ${{ job.status }}\nCommit: ${{ github.sha }}\nActor: ${{ github.actor
}}\nRun: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id
}}"
}

### env

SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

---

<!-- Source PDF page 17 -->

Scenario Workflow Behavior Notification
Tests fail Job fails, downstream jobs
skipped Slack: "Test: failure"
Build fails Job fails, deploy skipped Slack: "Build and Push: failure"
Terraform plan shows
deletion
Plan completes, deploy runs
anyway (staging only)
Slack: "Plan: success with changes" —
manual review needed
Health check fails on
staging deploy.sh rolls back, exits 1 Slack: "Deploy to Staging: failure"
Health check fails on
prod deploy.sh rolls back, exits 1 Slack: "PROD deploy: failure" — page
someone
Approval not given
within 24h Workflow auto-cancels Slack: "Release: cancelled"
If the deploy to prod fails, the rollback MUST also be automated. A 2 AM prod deploy that fails and requires
manual rollback is a postmortem in your future. The deploy.sh script in Section 5 handles this: on health
check failure, it reads the previous image from a file, restarts with that image, and exits non-zero. The next
morning you see the failed workflow, the rollback is already done, and you debug at your leisure. This is the
difference between a 3 AM emergency and a 10 AM postmortem.

## Failure Scenarios and What Should Happen


## The Single Most Important Failure Handling Rule


## Section 9 — Testing the Pipelines End to End


## The End-to-End Test Sequence

# 1. Create a feature branch
git checkout -b feature/improve-health-check
# 2. Make a small change
echo "# test change" >> app/__init__.py
git commit -am "test: trigger pipelines"
# 3. Push and open PR — PR pipeline runs
git push -u origin feature/improve-health-check
gh pr create --title "Test PR for pipeline" --body "Verifying PR pipeline"
# Watch: gh run watch
# Confirm: Lint, Test, Terraform Validate all pass
# 4. Merge the PR — main pipeline runs
gh pr merge --squash --delete-branch

---

<!-- Source PDF page 18 -->

Problem Cause Fix
OIDC login
fails
Federated credential subject
mismatch
Verify repo:owner/repo:ref:refs/heads/main
matches exactly
ACR login
fails
ACR name not in vars, or VM
identity missing AcrPull gh variable set ACR_NAME, verify role assignment
Terraform init
fails Backend config wrong Re-run terraform init locally with same backend
config
Deploy fails
at SSH
SSH key not in env-scoped
secret
gh secret set VM_SSH_PRIVATE_KEY --env staging
--body "..."
Health check
fails App crashes on new imageSSH in, docker compose logs app, fix the bug

## The Five Common Things That Go Wrong on First Run


## Quick Reference Card

# Watch: gh run watch
# Confirm: Test, Build, Plan, Deploy-to-staging all pass
# Confirm: Slack receives success notification
# 5. Verify staging
curl http://$(terraform -chdir=infra output -raw vm_public_ip)/health/live
# Should return {"status":"ok"}
# 6. Tag and push — release pipeline runs (will pause for approval)
git tag v1.0.0
git push --tags
# Watch: gh run watch
# Confirm: pipeline pauses at terraform-apply-prod, awaiting approval
# 7. Open GitHub UI, find the run, click "Review deployments" > Approve
# Confirm: terraform-apply-prod runs, then deploy-prod pauses for approval
# Approve again
# 8. Verify prod
curl http://<prod-vm-ip>/health/live
# Should return {"status":"ok"}
# 9. Verify Slack received both "staging success" and "PROD success" messages
┌─────────────────────────────────────────────────────────────────────────────┐
│             CAPSTONE CI/CD — QUICK REFERENCE                                │
├──────────────────────────────┬──────────────────────────────────────────────┤
│ PIPELINES (3)                │ pr.yml, main.yml, release.yml                │

---

<!-- Source PDF page 19 -->


## What you learned: OIDC is the modern auth pattern. No stored client secret means no rotation and no leak.

The federated credential subject is the security boundary — get it wrong and the whole pipeline is broken.

## What you learned: The PR pipeline is the first line of defense. Every bad commit caught here is a bad commit

that never reaches main. Every bad commit that reaches main is one that can reach prod.

## Hands-On Exercises


## Exercise 1 — Set Up Environments, Secrets, and OIDC

1. Run the gh api commands from Section 2 to create staging and production environments.
2. Add yourself as required reviewer on production.
3. Run the az ad app create and az ad app federated-credential create commands to set up
OIDC.
4. Verify the federated credential subject matches repo:yourusername/capstone:ref:refs/heads/main.
5. Run gh variable set and gh secret set for all values listed in Section 2.
6. Confirm: gh secret list and gh variable list show all expected entries.

## Exercise 2 — Write and Test the PR Pipeline

1. Create .github/workflows/pr.yml from Section 3.
2. Create a feature branch, push it, open a PR.
3. Watch the pipeline: gh run watch.
4. Confirm all three jobs (lint, test, terraform-validate) pass.
5. If any fail, fix the underlying issue in the app or infra code, push to the branch, watch again.
6. Once green, merge the PR.
7. Confirm in repo settings that the three jobs are marked as required status checks.
│ PR TRIGGER                   │ pull_request on main                         │
│ MAIN TRIGGER                 │ push on main                                 │
│ RELEASE TRIGGER              │ push tags v*                                 │
│ AZURE AUTH                   │ OIDC (no client secret, id-token: write)     │
│ ENVIRONMENTS (2)             │ staging (no approval), production (1 approver)│
│ COMPOSITE ACTION             │ .github/actions/deploy-to-vm/action.yml      │
│ DEPLOY MECHANISM             │ SSH + scripts/deploy.sh + docker compose     │
│ HEALTH CHECK                 │ /health/ready polled 60s, rollback on fail   │
│ ROLLBACK                     │ .previous_image file, sed swap, restart      │
│ NOTIFICATIONS                │ Slack webhook on every deploy (if: always)   │
│ IMAGE TAGS (staging)         │ sha-long, latest, main-N                     │
│ IMAGE TAGS (prod)            │ semver (v1.0.0), major.minor, prod           │
│ CONCURRENCY (PR)             │ cancel-in-progress: true                     │
│ CONCURRENCY (main/release)   │ cancel-in-progress: false                    │
│ APPROVAL GATES               │ terraform-apply-prod, deploy-prod (2 gates)  │
└──────────────────────────────┴──────────────────────────────────────────────┘

---

<!-- Source PDF page 20 -->


## What you learned: The main pipeline is the workhorse — every merge to main reaches staging automatically.

The composite action and deploy.sh together encode the deploy safety: pull, restart, health-check, rollback.
This is the artifact that proves you can ship.

## What you learned: The release pipeline is the most expensive pipeline to run because it touches prod. The

approval gate is not bureaucracy — it is the last chance to abort a bad release. The automated rollback is what
lets you sleep through a 2 AM deploy.
The pipelines are green. Tomorrow you make the system observable. You will add Prometheus and Grafana to
the docker-compose stack (locally and on the VM), commit a Grafana dashboard as JSON (dashboard-as-code),
configure Prometheus to scrape the Flask app's /metrics endpoint and the node-exporter on the VM, and
define at least two alerting rules with Alertmanager routing to Slack. By the end of Day 177, you can kill -9
the Flask process and watch a Slack alert fire within 60 seconds. That is the artifact due tomorrow night.

### Key commands for tomorrow


## Exercise 3 — Write the Main Pipeline and Deploy to Staging

1. Create .github/actions/deploy-to-vm/action.yml from Section 5.
2. Create scripts/deploy.sh from Section 5, chmod +x it.
3. Create .github/workflows/main.yml from Section 4.
4. Push a commit directly to main (or merge a PR).
5. Watch the pipeline through test, build, plan, deploy-staging.
6. Once green, curl http://<staging-ip>/health/live and verify {"status":"ok"}.
7. Confirm Slack received the success notification.

## Exercise 4 — Write the Release Pipeline and Deploy to Prod (with

Approval)

1. Create .github/workflows/release.yml from Section 6.
2. Tag v1.0.0: git tag v1.0.0 && git push --tags.
3. Watch the pipeline: gh run watch.
4. When it pauses at terraform-apply-prod, open the GitHub UI and approve.
5. When it pauses at deploy-prod, approve again.
6. Confirm prod is serving the new image: curl http://<prod-ip>/health/live.
7. Confirm Slack received "PROD deploy success: v1.0.0".
8. To test rollback: introduce a deliberate bug (e.g., make Flask crash on /health/ready), tag v1.0.1, push,
approve, watch deploy fail and rollback to v1.0.0 automatically.
What's Next — Day 177: Monitoring Setup for the Capstone
docker compose up -d prometheus grafana alertmanager — start the monitoring stack
curl http://localhost:9090/api/v1/targets | jq — verify Prometheus is scraping all targets
curl http://localhost:9090/api/v1/rules | jq — verify alert rules are loaded

---

<!-- Source PDF page 21 -->

EngiDock DevOps Training | Day 176 of 180
Build real. Break things. Learn why.
docker exec -it grafana grafana-cli admin reset-admin-password — set Grafana admin
password
amtool alert query — query active alerts from Alertmanager
