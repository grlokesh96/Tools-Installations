# SonarQube & Trivy — Shift-Left Security

**DevOps & Cloud Series · Day 4/100**

> SonarQube checks the code you write. Trivy checks what you ship.

This guide covers what SonarQube and Trivy do, the problems they solve, implementation examples, verification steps, and alternatives. Converted from the supplied PDF; review commands and version-specific details against your environment before using them in production.

## Table of Contents

- [Why Shift Left?](#why-shift-left)
- [Where Each Tool Fits in CI/CD](#where-each-tool-fits-in-cicd)
- [SonarQube](#sonarqube)
  - [What It Does](#what-it-does)
  - [Implementation](#sonarqube-implementation)
  - [Verify the Quality Gate](#verify-the-quality-gate)
- [Trivy](#trivy)
  - [What It Does](#what-it-does-1)
  - [Implementation](#trivy-implementation)
  - [Verify Trivy Scanning](#verify-trivy-scanning)
- [Pipeline Flow: Before vs. After](#pipeline-flow-before-vs-after)
- [Alternatives](#alternatives)
- [Quick Reference](#quick-reference)
- [Interview Answer](#interview-answer)

## Why Shift Left?

A bug found in the IDE may take minutes to fix. The same bug discovered in production can cause an incident, an emergency hotfix, or a data breach. **Shift left** means moving security and quality checks earlier in the delivery pipeline.

### Where Each Tool Fits in CI/CD

Typical flow:

1. Developer writes code.
2. Code is pushed and a pull request is opened.
3. SonarQube analyzes source code, code quality, and coverage.
4. Trivy scans the filesystem, dependencies, secrets, and Infrastructure as Code (IaC).
5. The application is built into a container image.
6. Trivy scans the image and can generate an SBOM.
7. The image is pushed to a registry and deployed.
8. Trivy Operator can continue scanning workloads in a Kubernetes cluster.

### Scan Types

| Scan type | What it checks | Example tool |
|---|---|---|
| Static Application Security Testing (SAST) | Source-code issues such as SQL injection, hardcoded passwords, and bugs | SonarQube |
| Software Composition Analysis (SCA) | Open-source libraries and known CVEs | Trivy |
| Container image scan | OS packages and libraries in an image | Trivy |
| IaC scan | Terraform, Kubernetes YAML, Helm, and Dockerfile misconfigurations | Trivy |
| Secret scan | API keys, tokens, and passwords committed accidentally | Trivy; SonarQube can also identify some issues |
| Code quality | Code smells, duplication, and test coverage | SonarQube |

**In short:** SonarQube asks, “Is my code good and safe?” Trivy asks, “Is what I package and deploy safe?”

## SonarQube

### What It Does

SonarQube is a static code analysis platform from SonarSource. It analyzes source code without executing it and can report bugs, vulnerabilities, security hotspots, code smells, duplication, and test coverage. A **Quality Gate** evaluates configured conditions and determines whether the analysis passes.

### Problems It Helps Solve

| Without a quality gate | With SonarQube |
|---|---|
| Code review may miss SQL injection or null-pointer issues | Pull requests can be automatically analyzed, with issues tied to source lines |
| Hardcoded credentials may reach the main branch | Security findings can fail the gate |
| Test coverage may drop unnoticed | Coverage on new code can be enforced, for example at 80% or higher |
| Duplication and technical debt accumulate | Duplication, debt, and ratings are visible |
| Teams use inconsistent quality criteria | Teams can share a common Quality Gate |

### Key Terms

| Term | Meaning |
|---|---|
| Quality Profile | Rules that determine which checks run for a language |
| Quality Gate | Pass/fail conditions, such as no new vulnerabilities and coverage of at least 80% |
| Clean as You Code | Focuses gate conditions on new code so existing technical debt does not block every change |
| Security Hotspot | Risky code that requires human review; it is not necessarily a confirmed vulnerability |

The source lists Community Build, Developer, Enterprise, SonarQube Cloud, and SonarQube for IDE as options. Feature availability depends on the product and edition.

### SonarQube Implementation

#### 1. Start a local test server

```bash
# Quick local trial
docker run -d --name sonarqube -p 9000:9000 sonarqube:community
```

For production deployments, use an appropriately configured external PostgreSQL database and follow the current SonarQube deployment documentation.

Kubernetes example from the source:

```bash
helm repo add sonarqube https://SonarSource.github.io/helm-chart-sonarqube
helm install sonarqube sonarqube/sonarqube \
  --namespace sonarqube \
  --create-namespace

# Check the node requirement for embedded Elasticsearch
sudo sysctl -w vm.max_map_count=524288
```

> The Helm chart and deployment requirements may change. Confirm the current chart instructions before using this in a real cluster.

#### 2. Create a project and configure analysis

Create the project and token in SonarQube, then add `sonar-project.properties` to the repository:

```properties
sonar.projectKey=shop-orders
sonar.sources=src
sonar.tests=tests
sonar.python.coverage.reportPaths=coverage.xml
```

Adapt the project key, source paths, test paths, and coverage report path to your application.

#### 3. Run analysis in Jenkins and enforce the Quality Gate

```groovy
stage('SonarQube') {
    steps {
        withSonarQubeEnv('sonar') {
            sh 'sonar-scanner'
        }
    }
}

stage('Quality Gate') {
    steps {
        timeout(time: 5, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
    }
}
```

The Jenkins Quality Gate step requires the SonarQube server to be configured in Jenkins and a SonarQube webhook to reach Jenkins.

For a scanner-based workflow that waits for the gate:

```bash
sonar-scanner -Dsonar.qualitygate.wait=true
```

The command should return a non-zero exit status when the gate fails. For Maven projects, the source gives this example:

```bash
mvn verify sonar:sonar
```

#### 4. Block merges when checks fail

Configure GitHub branch protection or repository rules so the required SonarQube status check must pass before merging.

### Verify the Quality Gate

Use a **disposable test branch and non-production code** to verify that the configured checks behave as expected. Do not commit real credentials or exploitable code to a production branch.

Example patterns a scanner should identify:

```python
# Deliberately insecure examples for local testing only.
password = "SuperSecret123"  # hardcoded credential pattern
query = "SELECT * FROM users WHERE id=" + user_id  # possible SQL injection
```

Expected verification workflow:

| Check | Expected result |
|---|---|
| Open a pull request | Analysis findings appear in the pull request, if PR decoration is configured |
| Quality Gate evaluates failing conditions | Pipeline fails |
| Branch protection is enabled | Merge is blocked while required checks fail |
| Fix the issue and rerun analysis | Gate passes if all configured conditions are met |
| Query the project status API | The response reports the gate status for the project |
| Reduce coverage below the configured threshold | Gate fails if that coverage condition is enabled |

Example API endpoint shown in the source:

```text
/api/qualitygates/project_status?projectKey=shop-orders
```

### SonarQube Metrics to Track

| Metric | Example goal from the source |
|---|---:|
| New vulnerabilities or bugs on `main` | 0 |
| Coverage on new code | At least 80%, trending upward |
| Security hotspots reviewed | 100% |
| Reliability, Security, and Maintainability ratings | A |
| Production repositories enforcing the gate | All |

These are example targets, not universal defaults. Set thresholds that fit your project and risk profile.

## Trivy

### What It Does

Trivy is an open-source security scanner from Aqua Security. It can scan container images, filesystems, Git repositories, Infrastructure as Code, and Kubernetes workloads, and can generate Software Bills of Materials (SBOMs).

### What Trivy Can Find

- **Vulnerabilities (CVEs):** issues in OS packages and application dependencies such as npm, pip, Maven, and Go modules.
- **Misconfigurations:** risky settings in Terraform, Kubernetes YAML, Helm charts, and Dockerfiles.
- **Secrets:** possible API keys, cloud keys, tokens, and passwords in code.
- **Licenses:** license information for included dependencies.
- **SBOM inventory:** CycloneDX or SPDX package inventories.
- **Cluster findings:** reports about running workloads when Trivy Operator is deployed.

### Problems It Helps Solve

| Without Trivy | With Trivy |
|---|---|
| A base image contains many known CVEs | CI can fail when configured severity thresholds are exceeded |
| Teams cannot quickly identify where a vulnerable library is used | SBOMs help inventory packages across images |
| IaC includes risky cloud or Kubernetes configuration | Misconfigurations can be detected before deployment |
| A secret is accidentally committed | Secret scanning can flag it |
| Running images become vulnerable as new CVEs are published | Continuous or scheduled rescanning can identify new findings |

### Trivy Implementation

#### 1. Install the CLI

On macOS, the source shows:

```bash
brew install trivy
```

Other installation options include package managers and container-based usage. Follow the current official installation instructions for your platform.

#### 2. Scan the repository and IaC in a pull request

```bash
# Scan filesystem, dependencies, secrets, and misconfigurations.
trivy fs \
  --scanners vuln,secret,misconfig \
  --exit-code 1 \
  --severity HIGH,CRITICAL \
  .

# Scan Terraform / IaC configuration.
trivy config ./terraform
```

`--exit-code 1` makes the command return a failure status when matching findings are present. Review the chosen scanners, severity policy, and exclusions for your project.

#### 3. Scan the container image

```bash
# Fail on HIGH or CRITICAL findings, ignoring unfixed vulnerabilities.
trivy image \
  --exit-code 1 \
  --severity HIGH,CRITICAL \
  --ignore-unfixed \
  acme/orders:1.4.2
```

Use an image reference that exists in your environment. The example image name is illustrative.

#### 4. Generate an SBOM

```bash
trivy image \
  --format cyclonedx \
  --output sbom.json \
  acme/orders:1.4.2
```

Store or publish the SBOM as part of the release artifacts according to your team's artifact-retention policy.

#### 5. Add Trivy to Jenkins

```groovy
stage('Trivy') {
    steps {
        sh '''
          trivy image --exit-code 1 \
            --severity HIGH,CRITICAL \
            --ignore-unfixed "$IMAGE"
        '''
    }
}
```

Ensure the `IMAGE` environment variable is set to the image you intend to scan.

#### 6. Scan Kubernetes workloads with Trivy Operator

The source gives this Helm example:

```bash
helm repo add aqua https://aquasecurity.github.io/helm-charts
helm install trivy-operator aqua/trivy-operator \
  --namespace trivy-system \
  --create-namespace
```

Review the current chart documentation and cluster permissions before deploying the operator.

Accepted risks can be documented in `.trivyignore` with a clear reason and expiry date. Pin CI actions and other critical dependencies to reviewed versions or commit SHAs according to your supply-chain policy.

### Verify Trivy Scanning

Use a test repository, disposable image, and non-production cluster.

| Test | Expected result |
|---|---|
| Scan an intentionally outdated base image such as `python:3.8` | Known vulnerabilities are reported; the pipeline fails if they meet the configured policy |
| Replace it with an approved, maintained base image | Findings should decrease; the pipeline passes only if policy conditions are met |
| Add a fake AWS-key pattern in test code | Secret finding is reported if detected by the configured scanner |
| Scan Kubernetes YAML containing `privileged: true` | A relevant misconfiguration may be reported |
| Scan Terraform with a bucket missing required encryption settings | A misconfiguration may be reported, depending on the resource and rule set |
| Query vulnerability reports | Reports should be present when Trivy Operator has completed its scans |
| Query configuration audit reports | Findings for supported live-resource misconfigurations should be visible |

Example commands from the source:

```bash
kubectl get vulnerabilityreports -A
kubectl get configauditreports -A
kubectl get vulnerabilityreports -n shop -o wide
```

Illustrative output format:

```text
REPOSITORY   TAG    CRITICAL   HIGH   MEDIUM
acme/orders  1.4.2  0          0      3
```

The values above are examples, not actual scan results.

### Trivy Metrics to Track

| Metric | Example goal from the source |
|---|---:|
| Critical CVEs in production images | 0, trending down |
| Mean time to patch a new critical vulnerability | Under 7 days |
| Releases with an SBOM | 100% |
| Secrets leaked to Git | 0 |

## Pipeline Flow: Before vs. After

### Before: Security Checks Happen Late

```text
Developer push
     |
Build + unit tests
     |
Push image
     |
Deploy to production
     |
Weeks later: penetration test or incident finds SQL injection,
leaked credentials, or vulnerable packages
```

Typical consequences: fixes are expensive and rushed, and teams have limited visibility into what is running in production.

### After: Shift-Left Security

```text
Developer (SonarQube for IDE)
     |
Pull request
     +--> SonarQube Quality Gate
     +--> Trivy filesystem / dependency / secret / IaC scan
     |
Build container image
     +--> Trivy image scan
     +--> Generate SBOM
     |
Push image to registry
     |
Deploy
     |
Trivy Operator rescans running workloads
```

- Findings can be caught during code review.
- Pull requests can be blocked when required checks fail.
- Newly discovered vulnerabilities in existing images can be found by subsequent scans.
- Begin with a manageable warning policy if needed, fix the backlog, and then enforce blocking thresholds such as critical findings. Communicate the rollout clearly to teams.

## Alternatives

### Alternatives to SonarQube

| Tool | Category | Good fit for |
|---|---|---|
| GitHub CodeQL | SAST | GitHub-native code analysis, especially with GitHub Advanced Security |
| Semgrep | SAST | Fast scanning and custom rules; open-source core |
| Snyk Code | SAST / SaaS | Developer-focused IDE and pull-request feedback |
| Checkmarx, Veracode, Fortify | Enterprise SAST | Organizations with extensive compliance requirements |
| Codacy, DeepSource | Code quality / SaaS | Lightweight hosted code-quality workflows |
| SonarQube Cloud | Hosted SonarQube | Teams that do not want to operate a SonarQube server |

### Alternatives to Trivy

| Tool | Category | Good fit for |
|---|---|---|
| Grype + Syft (Anchore) | Image scanning + SBOM | Open-source, SBOM-focused workflows |
| Snyk Open Source / Container | SCA + image scanning | Dependency and container analysis with remediation support |
| Docker Scout | Image scanning | Docker Hub and Docker Desktop workflows |
| Amazon ECR scanning / Amazon Inspector | Registry vulnerability scanning | AWS-oriented container workflows |
| Clair | Image scanning | Quay and Harbor registry ecosystems |
| Checkov / KICS | IaC scanning | Terraform, CloudFormation, and configuration policies |
| Gitleaks | Secret scanning | Detecting secrets before they are committed |
| Dependabot / OWASP Dependency-Check | Dependency security | Dependency updates and CVE detection |
| Prisma Cloud / Aqua / Wiz | Cloud-native application protection | Broader cloud and runtime security |

Choose tools based on language and platform support, required integrations, licensing, policy needs, operating cost, and the team's ability to maintain them.

## Quick Reference

| Area | SonarQube | Trivy |
|---|---|---|
| Main purpose | Static analysis and code quality | Vulnerability, secret, IaC, and image scanning; SBOM generation |
| Typical checks | Bugs, vulnerabilities, code smells, duplication, coverage | CVEs, misconfigurations, secrets, image packages, SBOM |
| CI integration | Scanner + Quality Gate | CLI scan with configured severity and exit-code policy |
| Kubernetes use | Not the focus of this guide | Trivy Operator can scan running workloads |
| Evidence that it works | A deliberately failing test PR is blocked; corrected code passes | A test image or configuration produces expected findings and the configured pipeline policy is enforced |

### Practical Tips

1. Gate on new code and agreed vulnerability thresholds to make the policy actionable.
2. Prefer maintained, minimal base images such as slim, distroless, or Chainguard images where suitable.
3. Generate an SBOM for every release.
4. Pin CI actions and scanner versions to reviewed versions or immutable SHAs where possible.
5. Track open critical findings, time to patch, and Quality Gate pass rate.
6. Treat scanner output as evidence to triage, not as a substitute for reviewing context and risk.

## Interview Answer

> In our Jenkins pipeline, SonarQube enforces a Quality Gate on new code, with policies such as zero new vulnerabilities and 80% coverage. Branch protection prevents merging when required checks fail. Trivy scans dependencies, Infrastructure as Code, and secrets on pull requests, scans container images for HIGH and CRITICAL vulnerabilities, and generates an SBOM. Trivy Operator can also rescan running Kubernetes workloads so newly discovered vulnerabilities are identified after deployment.

Adapt this answer to reflect the tools, policies, and work you have personally implemented.

---

**Secure code + secure image = secure release.**
