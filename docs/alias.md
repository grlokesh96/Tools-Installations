# CKA Aliases Cheat Sheet

```bash
# -----------------------------
# Kubernetes
# -----------------------------
alias k='kubectl'

# -----------------------------
# Get Resources
# -----------------------------
alias kg='kubectl get'
alias kgp='kubectl get pods'
alias kgn='kubectl get nodes'
alias kgd='kubectl get deployments'
alias kgrs='kubectl get rs'
alias kgrc='kubectl get rc'
alias kgs='kubectl get svc'
alias kging='kubectl get ingress'
alias kgns='kubectl get ns'
alias kgcm='kubectl get configmap'
alias kgsec='kubectl get secret'
alias kgpv='kubectl get pv'
alias kgpvc='kubectl get pvc'
alias kgsa='kubectl get sa'
alias kgep='kubectl get endpoints'
alias kgep='kubectl get ep'
alias kga='kubectl get all'

# Wide / YAML
alias kgpw='kubectl get pods -o wide'
alias kgy='kubectl get -o yaml'
alias kgoy='kubectl get -o yaml'

# -----------------------------
# Describe
# -----------------------------
alias kd='kubectl describe'
alias kdp='kubectl describe pod'
alias kdn='kubectl describe node'
alias kdd='kubectl describe deployment'
alias kdrs='kubectl describe rs'
alias kds='kubectl describe svc'

# -----------------------------
# Create / Apply / Delete
# -----------------------------
alias ka='kubectl apply -f'
alias kaf='kubectl apply -f'
alias kdel='kubectl delete'
alias kdf='kubectl delete -f'
alias kcf='kubectl create -f'

# -----------------------------
# Edit
# -----------------------------
alias ke='kubectl edit'
alias kep='kubectl edit pod'
alias ked='kubectl edit deployment'
alias kers='kubectl edit rs'
alias kens='kubectl edit namespace'

# -----------------------------
# Logs / Exec
# -----------------------------
alias kl='kubectl logs'
alias klf='kubectl logs -f'
alias keit='kubectl exec -it'

# -----------------------------
# Scale / Rollout
# -----------------------------
alias ks='kubectl scale'
alias kro='kubectl rollout'
alias kros='kubectl rollout status'
alias krou='kubectl rollout undo'
alias kroh='kubectl rollout history'
alias kror='kubectl rollout restart'

# -----------------------------
# Explain
# -----------------------------
alias kex='kubectl explain'

# -----------------------------
# Context / Namespace
# -----------------------------
alias kctx='kubectl config current-context'
alias kuse='kubectl config use-context'
alias kns='kubectl config set-context --current --namespace'

# -----------------------------
# Top
# -----------------------------
alias ktp='kubectl top pods'
alias ktn='kubectl top nodes'

# -----------------------------
# CKA Exam Generators
# -----------------------------
alias krun='kubectl run --dry-run=client -o yaml'
alias kdep='kubectl create deployment --dry-run=client -o yaml'
alias ksvc='kubectl expose --dry-run=client -o yaml'

# -----------------------------
# Useful
# -----------------------------
alias ksys='kubectl get pods -n kube-system'
alias kw='watch kubectl get pods'
alias knr='kubectl get nodes -o wide'
```

---

## Enable Bash Autocomplete (Huge Time Saver)

```bash
source <(kubectl completion bash)
complete -F __start_kubectl k
```

---

## Add Permanently

```bash
echo "alias k='kubectl'" >> ~/.bashrc
source ~/.bashrc

echo "source <(kubectl completion bash)" >> ~/.bashrc
echo "complete -F __start_kubectl k" >> ~/.bashrc
source ~/.bashrc
```

---

## Commands You Should Memorize for the CKA Exam

### Generate a Pod YAML

```bash
k run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
```

### Generate a Deployment YAML

```bash
k create deployment nginx --image=nginx --dry-run=client -o yaml > deploy.yaml
```

### Generate a Service YAML

```bash
k expose pod nginx --port=80 --target-port=80 --dry-run=client -o yaml > svc.yaml
```

### Scale

```bash
k scale deployment nginx --replicas=5
```

### Expose Deployment

```bash
k expose deployment nginx --port=80 --target-port=8080 --type=NodePort
```

### Port Forward

```bash
k port-forward pod/nginx 8080:80
```

### Copy Files

```bash
k cp file.txt pod-name:/tmp/
```

### Execute Inside a Pod

```bash
k exec -it nginx -- sh
```

### View Logs

```bash
k logs nginx
k logs -f nginx
```

### Check Events

```bash
k get events --sort-by=.metadata.creationTimestamp
```

### JSONPath Examples

```bash
k get nodes -o jsonpath='{.items[*].metadata.name}'

k get pods -o wide

k get pod nginx -o yaml
```


### aliases I recommend memorizing are:
```bash
k → kubectl
kgp → kubectl get pods
kdp → kubectl describe pod
ke → kubectl edit
ka → kubectl apply -f
kdel → kubectl delete
kl → kubectl logs
keit → kubectl exec -it
ks → kubectl scale
kex → kubectl explain
```
