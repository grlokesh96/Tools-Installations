# 🤖 100 Battle-Tested Claude Prompts for Kubernetes in 2026 🤖

> **The ultimate AI-assisted Kubernetes cheat sheet for engineers, SREs, and platform teams.**
> Stop re-typing the same questions. Copy, paste, get answers.

---

## 📖 About This Collection

This is a curated collection of **100 battle-tested Claude prompts** designed specifically for Kubernetes work in 2026. Whether you're debugging a crashing pod at 2 AM, designing a multi-tenant platform, or preparing for the CKA/CKAD/CKS exam, these prompts give you a running start.

**Who this is for:**
- Kubernetes beginners learning the ropes
- Mid-level engineers solving day-to-day operational problems
- Senior engineers and architects designing production systems
- SREs on-call and debugging under pressure
- Platform engineers building internal developer platforms
- CKA/CKAD/CKS exam candidates

**What's inside:** 10 categories × 10 prompts = 100 total prompts, covering everything from pod basics to GitOps, AI-native workloads, and multi-cluster federation.

---

## 🗂️ Categories

| # | Category | Prompts |
|---|----------|---------|
| 1 | [🏗️ Cluster Architecture & Setup](#1--cluster-architecture--setup) | 1–10 |
| 2 | [🐛 Debugging & Troubleshooting](#2--debugging--troubleshooting) | 11–20 |
| 3 | [🔐 Security & RBAC](#3--security--rbac) | 21–30 |
| 4 | [🌐 Networking & Services](#4--networking--services) | 31–40 |
| 5 | [💾 Storage & Persistence](#5--storage--persistence) | 41–50 |
| 6 | [📈 Scaling & Performance](#6--scaling--performance) | 51–60 |
| 7 | [🔄 CI/CD & GitOps](#7--cicd--gitops) | 61–70 |
| 8 | [🧪 CKA/CKAD/CKS Exam Prep](#8--ckaackadcks-exam-prep) | 71–80 |
| 9 | [🤖 AI/ML Workloads on Kubernetes](#9--aiml-workloads-on-kubernetes) | 81–90 |
| 10 | [🌍 Multi-Cluster & Platform Engineering](#10--multi-cluster--platform-engineering) | 91–100 |

---

## How to Use

1. **Find your category** from the table above.
2. **Copy the prompt** exactly as written (or lightly customize the `[bracketed placeholders]`).
3. **Paste into Claude** and get a precise, context-aware answer.
4. **Iterate** — follow up with Claude using the suggested follow-ups at the end of each section.

> **Tip:** Paste your actual YAML or error output directly into the prompt for the most accurate responses. Claude works best with real context.

---

## 1 — 🏗️ Cluster Architecture & Setup

**Prompts 1–10** help you understand, plan, and bootstrap Kubernetes clusters.

---

**Prompt 1 — Explain the control plane**
```
Explain the Kubernetes control plane components (kube-apiserver, etcd, kube-scheduler,
kube-controller-manager) and what would break if each one failed. Use a table format.
```

**Prompt 2 — kubeadm bootstrap checklist**
```
Give me a production-ready kubeadm cluster bootstrap checklist for Kubernetes 1.30+
on Ubuntu 22.04. Include pre-flight checks, CNI selection, and post-install hardening steps.
```

**Prompt 3 — etcd backup and restore**
```
Write step-by-step commands to back up etcd using etcdctl and restore it on a kubeadm cluster.
Include the environment variables needed and how to verify the restore was successful.
```

**Prompt 4 — Compare managed Kubernetes**
```
Compare EKS, GKE, and AKS for a team running 50 microservices in 2026.
Cover pricing model, default CNI, node auto-provisioning, upgrade strategy, and managed control plane SLA.
```

**Prompt 5 — kind vs k3s vs minikube**
```
I need a local Kubernetes environment for CKA exam practice. Compare kind, k3s (k3d), and minikube
for this use case. Recommend one and give me the exact setup commands.
```

**Prompt 6 — Multi-node kind cluster config**
```
Write a kind cluster config YAML with 1 control-plane node and 3 worker nodes.
Enable port mappings for ingress (80, 443) and mount a host directory into a worker node.
```

**Prompt 7 — Upgrade a kubeadm cluster**
```
Walk me through upgrading a kubeadm cluster from Kubernetes 1.29 to 1.30.
Include draining nodes, upgrading kubeadm/kubelet/kubectl, and un-cordoning. Highlight what can go wrong.
```

**Prompt 8 — Node labeling strategy**
```
Design a node labeling and tainting strategy for a cluster with: GPU nodes, high-memory nodes,
spot/preemptible nodes, and dedicated nodes for a compliance workload. Show example YAML.
```

**Prompt 9 — Cluster component health check**
```
Give me a set of kubectl and curl commands to health-check every control plane component
on a kubeadm cluster. Include what a healthy vs unhealthy response looks like for each.
```

**Prompt 10 — Cluster capacity planning**
```
Help me estimate the number of nodes needed for a cluster running 200 pods, each requesting
500m CPU and 512Mi memory. Assume node size is 4 vCPU / 8 GiB. Account for system overhead and DaemonSets.
```

---

## 2 — 🐛 Debugging & Troubleshooting

**Prompts 11–20** for diagnosing and fixing real Kubernetes problems fast.

---

**Prompt 11 — CrashLoopBackOff diagnosis**
```
My pod is in CrashLoopBackOff. Walk me through a systematic debugging process using kubectl commands.
What are the top 5 causes and how do I rule out each one?
```

**Prompt 12 — Paste and diagnose**
```
Here is output from `kubectl describe pod <name>` and `kubectl logs <name> --previous`.
Diagnose what is wrong and suggest a fix.

[PASTE YOUR DESCRIBE OUTPUT HERE]
[PASTE YOUR LOG OUTPUT HERE]
```

**Prompt 13 — ImagePullBackOff**
```
A pod is stuck in ImagePullBackOff. List every possible cause (registry auth, image name typo,
private registry, rate limiting, network policy) and give me the kubectl commands to diagnose each.
```

**Prompt 14 — Pending pod**
```
A pod has been in Pending state for 10 minutes. Give me a decision tree to find the root cause.
Cover: insufficient resources, taints/tolerations, node selector mismatch, PVC not bound, scheduler issues.
```

**Prompt 15 — OOMKilled**
```
My container keeps getting OOMKilled. How do I find the right memory limit?
Show me how to use kubectl top, metrics-server, and VPA recommendations to right-size the container.
```

**Prompt 16 — Service not reachable**
```
A Kubernetes Service is not reachable from within the cluster. Walk me through debugging:
DNS resolution, endpoint slice population, kube-proxy rules, and network policy.
Include the exact commands.
```

**Prompt 17 — Node NotReady**
```
A worker node is in NotReady state. Give me a runbook to diagnose and recover it.
Cover: kubelet status, container runtime, CNI plugin, disk pressure, and certificate expiry.
```

**Prompt 18 — etcd slow/unhealthy**
```
I'm seeing high etcd latency alerts. What are the common causes, how do I diagnose them
with etcdctl and metrics, and what are the remediation steps?
```

**Prompt 19 — Debug with ephemeral containers**
```
Show me how to attach an ephemeral debug container to a running pod that has no shell.
Use the `kubectl debug` command with a busybox image and explain what you can investigate inside.
```

**Prompt 20 — Events-driven debugging**
```
Write a one-liner to watch all Kubernetes events cluster-wide, sorted by time,
filtered to Warning events only. Then explain the most common Warning event reasons and what they mean.
```

---

## 3 — 🔐 Security & RBAC

**Prompts 21–30** for hardening, access control, and compliance.

---

**Prompt 21 — Least-privilege ServiceAccount**
```
Create a least-privilege ServiceAccount, Role, and RoleBinding for a pod that only needs to
list and get Pods and ConfigMaps in the "app" namespace. Write the YAML and explain each field.
```

**Prompt 22 — Audit an existing RBAC config**
```
Here is my ClusterRole and ClusterRoleBinding YAML. Identify any over-permissioned rules
and suggest a least-privilege replacement.

[PASTE YOUR YAML HERE]
```

**Prompt 23 — Pod Security Standards**
```
Explain Kubernetes Pod Security Standards (Privileged, Baseline, Restricted) in 2026.
Show me how to enforce the "restricted" profile on a namespace using labels, and what pod specs will be rejected.
```

**Prompt 24 — NetworkPolicy allow/deny**
```
Write a NetworkPolicy that:
- Denies all ingress and egress by default in namespace "production"
- Allows ingress on port 8080 only from pods with label app=frontend
- Allows egress to kube-dns on UDP 53
Include comments explaining each rule.
```

**Prompt 25 — Secrets management best practices**
```
Compare these approaches to secrets in Kubernetes in 2026: native Secrets (base64),
Sealed Secrets, External Secrets Operator (ESO) with Vault, and CSI Secrets Store.
Recommend one for a team on AWS using EKS, and show a minimal example.
```

**Prompt 26 — Certificate rotation**
```
How do I check when kubeadm cluster certificates expire and rotate them before they do?
Give me the exact commands and explain what breaks if certificates expire.
```

**Prompt 27 — Admission controllers**
```
Explain what admission controllers are in Kubernetes. List the most important ones enabled by default,
and explain how OPA Gatekeeper or Kyverno replaces them for policy-as-code in 2026.
```

**Prompt 28 — CIS Kubernetes Benchmark**
```
What are the top 10 checks from the CIS Kubernetes Benchmark 1.9 that teams most commonly fail?
For each, show how to remediate it with a kubectl command or config change.
```

**Prompt 29 — Image signing and verification**
```
Explain how to enforce signed container images in a Kubernetes cluster in 2026 using Cosign and Kyverno.
Show the end-to-end workflow: signing an image, pushing to a registry, and the Kyverno policy that verifies it.
```

**Prompt 30 — Falco runtime security**
```
I want to detect if any container in my cluster tries to run a shell or write to /etc.
Show me how to set up Falco as a DaemonSet and write a custom rule for these two scenarios.
```

---

## 4 — 🌐 Networking & Services

**Prompts 31–40** for understanding and troubleshooting Kubernetes networking.

---

**Prompt 31 — CNI comparison**
```
Compare Calico, Cilium, and Flannel as Kubernetes CNI plugins in 2026.
Cover: eBPF support, NetworkPolicy enforcement, performance, and when to choose each.
```

**Prompt 32 — Service types explained**
```
Explain ClusterIP, NodePort, LoadBalancer, and ExternalName Kubernetes Services with a real-world
analogy for each. Show a YAML example and describe when NOT to use each type.
```

**Prompt 33 — Ingress vs Gateway API**
```
Explain the difference between the Kubernetes Ingress API and the Gateway API (GA in 1.28+).
When should I migrate to Gateway API in 2026? Show a side-by-side YAML example for the same HTTP route.
```

**Prompt 34 — DNS debugging**
```
A pod cannot resolve external DNS names but can reach IPs directly. Give me a step-by-step
debugging guide using nslookup, dig, and kubectl exec. Include how to check CoreDNS logs and config.
```

**Prompt 35 — Headless service use cases**
```
When should I use a headless Service in Kubernetes? Explain with a StatefulSet example and show
how DNS records differ between a regular ClusterIP service and a headless service.
```

**Prompt 36 — Ingress with TLS**
```
Write a complete example: Nginx Ingress Controller with TLS termination using a cert-manager
Let's Encrypt certificate. Include the Ingress YAML, Certificate resource, and ClusterIssuer.
```

**Prompt 37 — Service mesh comparison**
```
Compare Istio, Linkerd, and Cilium Service Mesh for a 2026 production cluster with 100 services.
Cover: resource overhead, mTLS, traffic management, observability, and operational complexity.
```

**Prompt 38 — kube-proxy modes**
```
Explain iptables vs IPVS vs eBPF (Cilium) kube-proxy modes. What are the performance implications
at 1000 Services? How do I check which mode my cluster is using?
```

**Prompt 39 — Endpoint troubleshooting**
```
A Service has no endpoints. Walk me through debugging: selector mismatch, pod readiness, 
EndpointSlice controller, and network policy blocking. Show the exact kubectl commands.
```

**Prompt 40 — Multi-port service**
```
Write a Kubernetes Service and Deployment YAML for an app that exposes HTTP on port 8080
and metrics on port 9090. Show how to reference named ports in a NetworkPolicy and in a ServiceMonitor.
```

---

## 5 — 💾 Storage & Persistence

**Prompts 41–50** for volumes, PVCs, StorageClasses, and stateful apps.

---

**Prompt 41 — PV/PVC/StorageClass explained**
```
Explain PersistentVolumes, PersistentVolumeClaims, and StorageClasses in Kubernetes with a simple
analogy. Draw the binding lifecycle using ASCII art or a table. Show a YAML example for each.
```

**Prompt 42 — StatefulSet with PVC**
```
Write a StatefulSet YAML for a 3-replica Postgres cluster using volumeClaimTemplates.
Explain what happens to PVCs when you scale down the StatefulSet and how to recover data.
```

**Prompt 43 — Storage class for AWS EKS**
```
Write a StorageClass for AWS EKS using the EBS CSI driver with gp3 volume type, 
encryption enabled, and WaitForFirstConsumer binding mode. Show a matching PVC.
```

**Prompt 44 — RWX storage options**
```
What are my options for ReadWriteMany (RWX) storage in Kubernetes in 2026?
Compare NFS, AWS EFS CSI, CephFS, and Longhorn. When should I avoid RWX?
```

**Prompt 45 — Volume snapshot and restore**
```
Show me how to take a VolumeSnapshot of a PVC using the CSI snapshot controller,
then restore it into a new PVC. Include the SnapshotClass, VolumeSnapshot, and restore PVC YAML.
```

**Prompt 46 — emptyDir vs ConfigMap vs Secret volumes**
```
Compare emptyDir, ConfigMap, and Secret volume types. For each, show a YAML mount example
and explain the security implications and when to use each.
```

**Prompt 47 — Resizing a PVC**
```
How do I increase the size of a PVC from 10Gi to 50Gi in Kubernetes?
What does the StorageClass need to support? Will the pod restart? Show the exact commands.
```

**Prompt 48 — Backup with Velero**
```
Give me a getting-started guide for Velero on a Kubernetes cluster backed by S3.
Include: install commands, creating a backup schedule, restoring a namespace, and verifying integrity.
```

**Prompt 49 — Database on Kubernetes**
```
Should I run a production database on Kubernetes in 2026? Give me the honest pros and cons,
when it makes sense (e.g., with an operator), and when to use a managed DB service instead.
```

**Prompt 50 — Troubleshoot PVC stuck in Pending**
```
My PVC has been in Pending state for 15 minutes. Walk me through all the possible causes:
no matching PV, StorageClass misconfiguration, CSI driver issue, and zone mismatch.
Give me the kubectl commands for each check.
```

---

## 6 — 📈 Scaling & Performance

**Prompts 51–60** for HPA, VPA, KEDA, resource optimization, and performance tuning.

---

**Prompt 51 — HPA explained**
```
Write a complete HorizontalPodAutoscaler example that scales a deployment based on both
CPU (70% target) and a custom metric (requests-per-second from Prometheus).
Explain the stabilization window and scale-down behavior.
```

**Prompt 52 — VPA setup and recommendation**
```
Explain Vertical Pod Autoscaler modes (Off, Initial, Auto). Show a VPA object for a Deployment
in "Off" mode to get resource recommendations, then how to read those recommendations.
```

**Prompt 53 — KEDA event-driven scaling**
```
Show a KEDA ScaledObject that scales a consumer Deployment from 0 to 20 replicas
based on a Kafka topic lag > 100 messages. Include the TriggerAuthentication for the Kafka credentials.
```

**Prompt 54 — Resource requests and limits**
```
Explain the difference between resource requests and limits in Kubernetes.
What is the QoS class for Guaranteed, Burstable, and BestEffort pods?
What happens during node memory pressure for each class?
```

**Prompt 55 — Right-sizing containers**
```
I have a Deployment with requests: 2 CPU / 4Gi memory but the app uses 200m CPU / 512Mi on average.
Walk me through using kubectl top, Prometheus, and VPA to safely right-size it without causing OOMKills.
```

**Prompt 56 — Cluster Autoscaler vs Karpenter**
```
Compare Cluster Autoscaler and Karpenter for node provisioning in 2026 on EKS.
Cover: provisioning speed, cost optimization, bin-packing, spot instance support, and configuration complexity.
```

**Prompt 57 — Pod Disruption Budget**
```
Explain PodDisruptionBudgets and why they matter. Write a PDB that ensures at least 2 replicas
of a 4-replica deployment are always available during a node drain. Show what happens without a PDB.
```

**Prompt 58 — Affinity and anti-affinity**
```
Write a Deployment YAML with:
- Soft anti-affinity: prefer pods on different nodes
- Hard anti-affinity: never schedule two pods on the same zone
- Node affinity: only schedule on nodes with label disk=ssd
Explain required vs preferred rules.
```

**Prompt 59 — LimitRange and ResourceQuota**
```
Write a ResourceQuota and LimitRange for a "dev" namespace that: caps total CPU at 10 cores,
total memory at 20Gi, sets default container limits to 500m/512Mi, and limits to 20 pods total.
```

**Prompt 60 — Load test a Kubernetes app**
```
I want to load-test a Deployment and watch HPA scale it in real time. 
Show me how to run a load test with `k6` or `hey` inside a pod, and the kubectl watch commands
to observe HPA and pod count changing live.
```

---

## 7 — 🔄 CI/CD & GitOps

**Prompts 61–70** for pipelines, ArgoCD, Flux, and deployment strategies.

---

**Prompt 61 — GitOps explained**
```
Explain GitOps for Kubernetes in plain English. What problem does it solve compared to
push-based CI/CD? Compare ArgoCD and Flux v2 for a team of 5 engineers in 2026.
```

**Prompt 62 — ArgoCD Application YAML**
```
Write an ArgoCD Application manifest that deploys a Helm chart from a private GitHub repo
to the "production" namespace. Include automated sync, self-heal, and prune enabled.
Explain each spec field.
```

**Prompt 63 — Helm chart for a microservice**
```
Create a minimal but production-ready Helm chart for a stateless microservice. Include:
Deployment, Service, HPA, PodDisruptionBudget, and a values.yaml with sensible defaults.
```

**Prompt 64 — Blue-green deployment**
```
Explain blue-green deployments in Kubernetes. Show a manual blue-green implementation using
two Deployments and a Service selector switch. Then show how to do it automatically with Argo Rollouts.
```

**Prompt 65 — Canary deployment**
```
Show a canary deployment strategy in Kubernetes that routes 10% of traffic to a new version.
Use Argo Rollouts or Nginx Ingress traffic splitting. Include how to promote or rollback automatically.
```

**Prompt 66 — GitHub Actions → Kubernetes**
```
Write a GitHub Actions workflow that: builds a Docker image, pushes to GHCR,
updates the image tag in a Kubernetes Deployment using kubectl, and rolls back if the rollout fails.
```

**Prompt 67 — Kustomize overlays**
```
Show a Kustomize project structure with a base Deployment and two overlays: dev (1 replica, debug env)
and prod (3 replicas, resource limits, PDB). Explain when to use Kustomize vs Helm.
```

**Prompt 68 — Multi-environment promotion with GitOps**
```
Design a GitOps repository structure for promoting an app through dev → staging → production
using ArgoCD ApplicationSets. Show how a PR merge triggers automated dev deploy
and a manual gate before production.
```

**Prompt 69 — Rollback a bad deployment**
```
My last kubectl rollout introduced a bug. Walk me through rolling back to the previous version
using kubectl rollout undo, checking rollout history, and pausing a rollout mid-way.
```

**Prompt 70 — Policy-as-code in CI**
```
Show how to use conftest and OPA/Rego or Kyverno CLI to validate Kubernetes YAML manifests
in a CI pipeline before they are applied to the cluster. Include a sample policy that rejects
containers running as root.
```

---

## 8 — 🧪 CKA/CKAD/CKS Exam Prep

**Prompts 71–80** specifically for certification exam preparation.

---

**Prompt 71 — CKA exam task simulator**
```
Give me a CKA-style exam task with increasing difficulty. Start with: "Create a pod named 'web'
in namespace 'frontend' using image nginx:1.25, with a label app=web and a readiness probe on /healthz port 80."
Then grade my answer when I paste the YAML back.
```

**Prompt 72 — Speed drill: imperative commands**
```
I have 2 minutes. Give me 10 kubectl imperative commands I must memorize for the CKA exam,
each with a practical example. Focus on commands that are faster than writing YAML from scratch.
```

**Prompt 73 — YAML generation shortcuts**
```
Show me how to use --dry-run=client -o yaml to quickly generate boilerplate YAML for:
Pod, Deployment, Service, ConfigMap, ServiceAccount, Role, and RoleBinding. 
These are my CKA speed shortcuts.
```

**Prompt 74 — Tricky CKA scenarios**
```
Give me 5 tricky CKA exam scenarios that candidates commonly fail. For each, explain the trick,
the correct approach, and the exact command(s) to use.
```

**Prompt 75 — CKAD exam prep: multi-container pods**
```
Explain sidecar, ambassador, and adapter container patterns. Give me a CKAD-style task for each,
with a complete pod YAML solution.
```

**Prompt 76 — CKS: harden a pod spec**
```
Here is a pod spec running with root privileges and no security context.
Rewrite it to pass CKS standards: non-root user, read-only root filesystem,
dropped capabilities, no privilege escalation, and a seccomp profile.

[PASTE YOUR POD YAML HERE]
```

**Prompt 77 — Exam time management**
```
Give me a time management strategy for the CKA exam (2 hours, ~17 tasks).
How should I prioritize tasks by weight? When should I skip and come back?
What are the first 5 things to do after the exam starts?
```

**Prompt 78 — kubectl context switching**
```
The CKA exam has multiple clusters. Show me exactly how to switch between kubectl contexts,
verify the current context, and set a namespace default to avoid mistakes under time pressure.
```

**Prompt 79 — Mock exam question bank**
```
Give me a 10-question mock CKA exam with realistic tasks covering:
RBAC, etcd backup, network policy, node drain, persistent volume, and pod scheduling.
Format as a numbered list with expected completion time per task.
```

**Prompt 80 — Last-day revision prompt**
```
I'm taking the CKA exam tomorrow. Give me the most important 20 things to review tonight:
commands, concepts, YAML patterns, and common gotchas. Format as a revision checklist.
```

---

## 9 — 🤖 AI/ML Workloads on Kubernetes

**Prompts 81–90** for running AI and ML workloads on Kubernetes in 2026.

---

**Prompt 81 — GPU scheduling on Kubernetes**
```
Explain how to schedule GPU workloads on Kubernetes in 2026. Cover: NVIDIA device plugin,
requesting nvidia.com/gpu resources, GPU node labeling, and time-slicing for multiple pods per GPU.
Show a pod YAML that requests 1 GPU.
```

**Prompt 82 — Kubeflow Pipelines overview**
```
Explain Kubeflow Pipelines in 2026. What problem does it solve for ML teams?
Show a minimal pipeline YAML and explain how it differs from a regular Kubernetes Job.
```

**Prompt 83 — Running LLM inference on Kubernetes**
```
I want to deploy an open-source LLM (e.g., Llama 3) on Kubernetes for inference.
What are my options in 2026? Compare vLLM, Ollama, and Ray Serve. 
Show a minimal Deployment YAML for a model server with GPU, health checks, and resource limits.
```

**Prompt 84 — KServe for model serving**
```
Explain KServe (formerly KFServing). Show a KServe InferenceService YAML that serves
a Hugging Face model from S3. Include autoscaling from 0 and canary traffic splitting.
```

**Prompt 85 — Volcano or KWOK for batch AI jobs**
```
I have batch AI training jobs that need gang scheduling (all-or-nothing pod allocation).
Explain Volcano and how its Queue and Job resources differ from Kubernetes batch/v1 Jobs.
Show a Volcano job YAML for a distributed PyTorch training run.
```

**Prompt 86 — Ray on Kubernetes**
```
Show how to deploy a Ray cluster on Kubernetes using the KubeRay operator.
Include a RayCluster resource YAML with head and worker nodes, GPU workers, and autoscaling.
```

**Prompt 87 — Resource quotas for AI teams**
```
Design a multi-tenant resource quota strategy for an AI platform where 3 teams share
a GPU cluster. Show ResourceQuota and LimitRange YAMLs per namespace, and explain
how to prevent one team from monopolizing all GPUs.
```

**Prompt 88 — Persistent storage for ML experiments**
```
What storage strategy should I use for ML experiment artifacts (datasets, checkpoints, logs)
on Kubernetes in 2026? Compare S3-compatible object storage vs PVCs vs distributed file systems
for large model training.
```

**Prompt 89 — Monitoring GPU utilization**
```
How do I monitor GPU utilization in a Kubernetes cluster in 2026?
Show how to set up DCGM Exporter as a DaemonSet, scrape it with Prometheus,
and create a Grafana dashboard panel for GPU memory and compute utilization.
```

**Prompt 90 — Jupyter notebooks on Kubernetes**
```
Show how to deploy JupyterHub on Kubernetes using the official Helm chart.
Cover: authentication (GitHub OAuth), resource profiles per user, persistent home directories,
and spawning GPU-enabled notebook servers on demand.
```

---

## 10 — 🌍 Multi-Cluster & Platform Engineering

**Prompts 91–100** for platform teams managing multiple clusters and building internal platforms.

---

**Prompt 91 — Multi-cluster strategy**
```
Explain the main multi-cluster Kubernetes architectures in 2026: hub-spoke, federated, and mesh.
When should I choose each? What tooling (Admiralty, Liqo, Cluster API) supports each model?
```

**Prompt 92 — Cluster API (CAPI) bootstrap**
```
Give me a getting-started walkthrough for Cluster API on AWS (CAPA).
What is the management cluster? Show the commands to provision a workload cluster
and how to upgrade it using CAPI.
```

**Prompt 93 — ArgoCD ApplicationSets for fleet management**
```
Show an ArgoCD ApplicationSet that deploys the same Helm chart to 10 clusters
using a Git generator. Each cluster has its own values file in a values/ directory.
Explain how to add a new cluster with zero ArgoCD config changes.
```

**Prompt 94 — Internal Developer Platform design**
```
I'm building an Internal Developer Platform (IDP) on Kubernetes in 2026.
Compare Backstage, Kratix, and Crossplane as the foundation. What does a good IDP
give developers, and what's the platform team's responsibility?
```

**Prompt 95 — Crossplane for infrastructure**
```
Explain Crossplane's Composition model. Show a Composition that creates an RDS instance
and an S3 bucket when a developer applies a single custom resource called `AppDatabase`.
Include the XRD, Composition, and example claim YAML.
```

**Prompt 96 — Multi-cluster observability**
```
Design an observability stack for 5 Kubernetes clusters in 2026.
Compare: federated Prometheus with Thanos, Grafana Mimir, and Victoria Metrics.
Include how to correlate traces across clusters with OpenTelemetry.
```

**Prompt 97 — Workload identity federation**
```
Explain workload identity federation in Kubernetes in 2026 (e.g., EKS IAM Roles for Service Accounts,
GKE Workload Identity). Show the setup for a pod that reads from an S3 bucket
without storing any credentials in the cluster.
```

**Prompt 98 — Cost optimization across clusters**
```
Give me 10 actionable Kubernetes cost optimization techniques for 2026.
Cover: right-sizing, spot instances, Karpenter consolidation, namespace quotas,
unused resource cleanup, and chargeback reporting with OpenCost.
```

**Prompt 99 — Kubernetes 2026 feature roundup**
```
What are the most significant Kubernetes features that graduated to stable or beta 
between versions 1.28 and 1.31? Which ones should a platform engineer adopt immediately
and which should they wait on? Format as a table with version, feature, and recommendation.
```

**Prompt 100 — Design review prompt**
```
Review my Kubernetes architecture design and give me feedback on reliability, security,
cost, and operational complexity. Here is my setup:

[DESCRIBE YOUR ARCHITECTURE OR PASTE YOUR DIAGRAM/YAML]

Rate each area out of 10, identify the top 3 risks, and give me a prioritized action plan.
```

---

## 💡 Tips for Getting the Most Out of Claude

### Always provide context
The more context you give, the better the answer. Add:
- Your Kubernetes version
- Cloud provider (EKS / GKE / AKS / bare metal)
- Actual YAML, error messages, or command output
- Your constraints (team size, budget, compliance requirements)

### Iterate, don't just accept
After the first answer, follow up with:
- `"Now write the YAML for that"`
- `"Show me what this looks like in a CI pipeline"`
- `"What could go wrong with this approach?"`
- `"Explain that like I'm new to Kubernetes"`
- `"Give me a simpler version"`

### Use Claude as a pair programmer
Paste your actual YAML or config and ask:
- `"What's wrong with this?"`
- `"How would you improve this?"`
- `"Is this production-ready? What's missing?"`

---

## 🤝 Contributing

Found a prompt that doesn't work well, or have a great new one?

1. Fork this repo
2. Add your prompt to the appropriate category (or propose a new one)
3. Follow the existing format: **bold title**, fenced code block, no fluff
4. Open a PR with a brief description of what the prompt does and when to use it

---

## 📜 License

MIT License — free to use, share, and adapt.

---

## 🌟 Related Resources

- [Kubernetes Official Docs](https://kubernetes.io/docs/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [CNCF Landscape](https://landscape.cncf.io/)
- [Killer.sh CKA Practice](https://killer.sh/)
- [KodeKloud Kubernetes Labs](https://kodekloud.com/)
- [CKA Exam Curriculum (CNCF)](https://github.com/cncf/curriculum)

---

<p align="center">
  <sub>100 prompts · 10 categories · 0 fluff · Built for Kubernetes engineers in 2026</sub>
</p>
