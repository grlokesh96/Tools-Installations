# AWS IAM Identity Provider for GitHub Actions (OIDC)

## Overview

To enable GitHub Actions to authenticate with AWS without using
long-lived AWS access keys, configure an OpenID Connect (OIDC) Identity
Provider in AWS IAM.

This allows GitHub Actions workflows to assume IAM roles securely using
short-lived credentials.

---

## Identity Provider Details

When creating the Identity Provider in AWS IAM, use the following values:

| Setting | Value |
|---|---|
| Provider Type | OpenID Connect (OIDC) |
| Provider URL | `https://token.actions.githubusercontent.com` |
| Audience | `sts.amazonaws.com` |

---

## Configuration

### Provider URL

```text
https://token.actions.githubusercontent.com
```

### Audience

```text
sts.amazonaws.com
```

---

## AWS Console Navigation

1. Sign in to the AWS Management Console.
2. Open **IAM**.
3. Select **Identity Providers**.
4. Click **Add Provider**.
5. Choose **OpenID Connect**.
6. Enter the Provider URL.
7. Enter the Audience (`sts.amazonaws.com`).
8. Click **Add Provider**.

---

## Trust Policy Example

After creating the provider, attach a trust policy to the IAM role that
GitHub Actions will assume:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        },
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:<ORG>/<REPO>:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

---

## Reference Repository

Replace the placeholders below with your GitHub repository URL.

**Repository:** `<YOUR_GITHUB_REPOSITORY_URL>`

Example:

```text
https://github.com/your-username/aws-github-actions-oidc
```

---

## References

- [GitHub Actions OpenID Connect (OIDC)](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)
- [AWS IAM Identity Providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)
- [AWS STS AssumeRoleWithWebIdentity](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html)
