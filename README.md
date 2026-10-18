# Tools Installations

A curated collection of DevOps, DevSecOps and Platform Engineering
resources — tool installation guides, CI/CD pipelines, Kubernetes/EKS
playbooks, Terraform workflows and Python automation scripts.

---

## Repository Structure

```text
Tools-Installations/
├── docs/
│   ├── installation/            # Install AWS CLI, kubectl, eksctl, Docker, Terraform, Argo CD
│   ├── kubernetes-eks/          # EKS, local Kubernetes, Argo CD, metrics-server, Lens
│   ├── cicd-terraform/          # GitHub Actions OIDC, Terraform CI/CD, Infracost, DevSecOps
│   ├── automation/              # Boto3, Lambda, Istio, DevSecOps Python automation
│   └── platform-engineering/    # AI-native Internal Developer Platform guide
├── scripts/
│   ├── install-tools.sh                     # Installs AWS CLI, eksctl, kubectl, Docker
│   ├── install-metrics-server.sh            # Installs & patches Metrics Server on EKS
│   ├── terraform-drift-detection.py         # Detects Terraform drift + Slack alert
│   └── ecr-image-cleanup.py                 # Cleans old images from ECR (dry-run support)
└── pipelines/
    └── devsecops-cicd.yml                   # Enterprise GitHub Actions DevSecOps pipeline
```

---

## Documentation

### [`docs/installation/`](docs/installation/)

| Guide | Description |
|---|---|
| [aws-cli-eksctl-kubectl.md](docs/installation/aws-cli-eksctl-kubectl.md) | Install AWS CLI v2, eksctl, kubectl and Terraform on Ubuntu |
| [docker-sonarqube.md](docs/installation/docker-sonarqube.md) | Install Docker Engine and run SonarQube in a container |
| [docker-ec2-user-data.md](docs/installation/docker-ec2-user-data.md) | EC2 User Data script that installs Docker at launch |
| [install-tools-script.md](docs/installation/install-tools-script.md) | Docs for the all-in-one `install-tools.sh` script |

### [`docs/kubernetes-eks/`](docs/kubernetes-eks/)

| Guide | Description |
|---|---|
| [argocd-installation.md](docs/kubernetes-eks/argocd-installation.md) | Install Argo CD on EKS and expose the UI via LoadBalancer |
| [ecr-eks-end-to-end.md](docs/kubernetes-eks/ecr-eks-end-to-end.md) | Build → push to ECR → deploy to EKS, scale, roll back |
| [local-k8s-deployment.md](docs/kubernetes-eks/local-k8s-deployment.md) | End-to-end deployment to a local multi-node Kubernetes cluster |
| [eks-lens-integration.md](docs/kubernetes-eks/eks-lens-integration.md) | Connect AWS EKS clusters to the Lens desktop IDE |
| [fix-metrics-api.md](docs/kubernetes-eks/fix-metrics-api.md) | Fix `error: Metrics API not available` on Amazon EKS |

### [`docs/cicd-terraform/`](docs/cicd-terraform/)

| Guide | Description |
|---|---|
| [github-actions-aws-oidc.md](docs/cicd-terraform/github-actions-aws-oidc.md) | Configure AWS IAM OIDC identity provider for GitHub Actions |
| [enterprise-terraform-cicd.md](docs/cicd-terraform/enterprise-terraform-cicd.md) | Enterprise Terraform CI/CD with GitHub Actions, OIDC, GitOps and drift detection |
| [terraform-devsecops-infracost.md](docs/cicd-terraform/terraform-devsecops-infracost.md) | Terraform DevSecOps pipeline with Infracost, OPA and security scanners |

### [`docs/automation/`](docs/automation/)

| Guide | Description |
|---|---|
| [aws-devops-boto3.md](docs/automation/aws-devops-boto3.md) | 15 production-ready AWS Boto3 automations (EC2, ECR, EBS, IAM, S3, EKS) |
| [devsecops-python-automation.md](docs/automation/devsecops-python-automation.md) | Python automation patterns for senior DevSecOps interviews |
| [istio-traffic-analysis.md](docs/automation/istio-traffic-analysis.md) | Query Prometheus for Istio traffic, errors and latency reports |
| [lambda-ec2-scheduler.md](docs/automation/lambda-ec2-scheduler.md) | Start/stop EC2 on a schedule with Lambda + EventBridge |

### [`docs/platform-engineering/`](docs/platform-engineering/)

| Guide | Description |
|---|---|
| [ai-native-platform-engineering.md](docs/platform-engineering/ai-native-platform-engineering.md) | Complete design & implementation guide for an AI-native Internal Developer Platform |

---

## Scripts

### Shell

```bash
# Install AWS CLI, eksctl, kubectl, Docker & Docker Compose (Ubuntu)
sudo ./scripts/install-tools.sh

# Install and patch Metrics Server on a Kubernetes/EKS cluster
./scripts/install-metrics-server.sh
```

### Python

```bash
# Detect Terraform drift and send a Slack alert
python3 scripts/terraform-drift-detection.py

# Clean old images from all ECR repositories (dry-run by default)
python3 scripts/ecr-image-cleanup.py
```

| Script | Purpose |
|---|---|
| [`scripts/install-tools.sh`](scripts/install-tools.sh) | One-shot installer for AWS CLI v2, eksctl, kubectl, Docker Engine and Docker Compose on Ubuntu |
| [`scripts/install-metrics-server.sh`](scripts/install-metrics-server.sh) | Installs Metrics Server, patches `--kubelet-insecure-tls` for lab/EKS clusters, verifies `kubectl top` |
| [`scripts/terraform-drift-detection.py`](scripts/terraform-drift-detection.py) | Runs `terraform plan -detailed-exitcode`, writes a drift report, notifies Slack |
| [`scripts/ecr-image-cleanup.py`](scripts/ecr-image-cleanup.py) | Keeps the latest N images per ECR repository, supports dry-run mode and logging |

---

## Pipelines

[`pipelines/devsecops-cicd.yml`](pipelines/devsecops-cicd.yml) — Enterprise-grade GitHub Actions DevSecOps pipeline:

```text
secret-scan (Gitleaks) ─┐
sast (Semgrep)          ├──▶ build-test ──▶ sca-trivy ──▶ docker-build ──▶
sonarqube ──────────────┘                                              │
                                                                       ▼
image-scan (Trivy) ──▶ sbom (Syft) ──▶ sign (Cosign) ──▶ push (ECR) ──▶ deploy-staging ──▶ dast (ZAP)
```

Highlights:

- Secret scanning, SAST and SonarQube run in parallel
- AWS authentication via GitHub OIDC only — no long-lived keys
- Keyless image signing with Sigstore Cosign
- Trivy image/filesystem scanning, Syft SBOM, ZAP DAST

---

## Prerequisites

Most guides assume an Ubuntu host with the following installed
(see [`docs/installation/`](docs/installation/)):

```bash
git --version
aws --version
kubectl version --client
eksctl version
docker --version
terraform version
helm version
```

Optional security/quality tools used by the pipelines:

```bash
tflint --version
checkov --version
trivy --version
gitleaks version
conftest --version
```

---

## Topics

`AWS` · `Amazon EKS` · `Kubernetes` · `Docker` · `Terraform` ·
`GitHub Actions` · `Argo CD` · `DevSecOps` · `Boto3` · `Istio` ·
`Infracost` · `OPA` · `Trivy` · `Gitleaks` · `Platform Engineering`
