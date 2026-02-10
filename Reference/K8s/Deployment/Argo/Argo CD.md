#k8s #deployment 

```table-of-contents
```
## Find apps not installed with Argo CD

``` powershell
kubectl get deployments -A -l '!app.kubernetes.io/instance'
```

---
## Argo CD Cilium causes Out-Of-Sync

Add this to the bottom of your `argocd-cm` ConfigMap:

``` yaml
data:
  resource.exclusions: |
    - apiGroups:
        - cilium.io
      kinds:
        - CiliumIdentity
```

Then restart the deployment "`argocd-applicationset-controller`".

---
## Argo CD ignore changes in requests or limits

To ignore changes in Requests & Limits for a single app, add the following to the Argo CD application manifest:

``` yaml
spec:
  ignoreDifferences:
    - group: "*"
      kind: "*"
      jqPathExpressions:
        - '.spec.template.spec.containers[].resources.requests'
        - '.spec.template.spec.containers[].resources.limits'
```

To ignore it globally add this to the `argocd-cm` ConfigMap:

``` yaml
data:
  resource.customizations: |
    apps/Deployment:
      ignoreDifferences: |
        jqPathExpressions:
          - .spec.template.spec.containers[].resources.requests
          - .spec.template.spec.containers[].resources.limits
    apps/StatefulSet:
      ignoreDifferences: |
        jqPathExpressions:
          - .spec.template.spec.containers[].resources.requests
          - .spec.template.spec.containers[].resources.limits
    apps/DaemonSet:
      ignoreDifferences: |
        jqPathExpressions:
          - .spec.template.spec.containers[].resources.requests
          - .spec.template.spec.containers[].resources.limits
    argoproj.io/Rollout:
	  ignoreDifferences: |
        jqPathExpressions:
          - .spec.template.spec.containers[].resources.requests
          - .spec.template.spec.containers[].resources.limits
```

---

## Argo CD password

You should delete the `argocd-initial-admin-secret` from the Argo CD namespace once you changed the password. The secret serves no other purpose than to store the initially generated password in clear and can safely be deleted at any time. It will be re-created on demand by Argo CD if a new admin password must be re-generated.

### Retrieving the install-time password

``` powershell
# Retrieve ArgoCD admin password from the Kubernetes secret
$secret = kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"

# Decode from Base64 and display the password
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($secret))
```

---

## Argo CD Repository Structure for Helm, Kustomize, and YAML Across Multiple Clusters

This guide outlines best practices for organizing a Git repository to support clean, scalable deployments with Argo CD using Helm charts, Kustomize overlays, and raw YAML manifests — across multiple Kubernetes clusters.

### Recommended Directory Structure

```
argocd01/
├── apps/
│   ├── clusters/
│   │   ├── tokyo/
│   │   │   ├── apps.yaml           # App-of-Apps or app group
│   │   │   ├── myapp.yaml
│   │   │   └── monitoring.yaml
│   │   └── paris/
│   │       ├── apps.yaml
│   │       └── myapp.yaml
├── helm/
│   └── myapp/
│       ├── chart/                 # Original Helm chart
│       ├── base-values.yaml       # Common values
│       ├── tokyo-values.yaml      # Cluster-specific overrides
│       └── paris-values.yaml
├── kustomize/
│   └── monitoring/
│       ├── base/
│       └── overlays/
│           ├── tokyo/
│           └── paris/
├── manifests/
│   └── cert-manager/              # Raw YAML apps
└── README.md
```

### Values Handling with Helm

You should:
- Leave the downloaded Helm chart untouched (`chart/`)
- Use separate `values.yaml` files (e.g. `tokyo-values.yaml`) to override only the settings you want to change
- All default values from the original `values.yaml` in the chart still apply unless explicitly overridden

Helm recursively merges the values from the base and override files.

### Example Argo CD Application with Layered Values

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  destination:
    name: tokyo                                  # Cluster name in Argo CD
    namespace: myapp
  source:
    repoURL: https://github.com/david-chase/argocd01
    targetRevision: HEAD
    path: helm/myapp/chart
    helm:
      valueFiles:
        - ../../../../helm/myapp/base-values.yaml
        - ../../../../helm/myapp/tokyo-values.yaml
  project: default
```

### Multi-Environment Layering

You can layer multiple `values.yaml` files:

```yaml
helm:
  valueFiles:
    - base-values.yaml
    - dev-values.yaml   # or prod-values.yaml
```

Later files override earlier ones.

### Mixing Helm, Kustomize, and YAML

Use subfolders like:

- `helm/` for charts and overrides
- `kustomize/` for Kustomize bases and overlays
- `manifests/` for raw Kubernetes YAML

### App-of-Apps Pattern (Optional)

Use a `root-app.yaml` to deploy all apps per cluster:

```yaml
spec:
  source:
    path: apps/clusters/tokyo
```

Argo CD will pick up each application file within that folder.

This structure supports scaling to many clusters, avoids chart mutations, and keeps the GitOps repo clean and predictable.

---

## Delete an app with stuck finalizer

``` powershell
kubectl patch app ingress-nginx -n argocd -p '{"metadata":{"finalizers":[]}}' --type=merge
```

---

## Install Argo CD

### Install the CLI

#### Linux
``` powershell
# Download the latest Argocd CLI binary
Invoke-WebRequest -Uri "https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64" -OutFile argocd-linux-amd64

# Make it executable and move into your PATH
sudo chmod 555 argocd-linux-amd64
sudo mv argocd-linux-amd64 /usr/local/bin/argocd

# Verify the installation
argocd version --client
```

#### Windows
``` powershell
choco install argocd-cli
```

### Deploy to cluster

``` powershell
kubectl create namespace argocd

# Install Argo CD core components
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Port forward the service

 - In a new PowerShell  window:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
### Login to the CLI

``` powershell
# 1. Grab the initial admin password
$base64Password = kubectl -n argocd get secret argocd-initial-admin-secret `
  -o jsonpath="{.data.password}"

$initialPassword = [System.Text.Encoding]::UTF8.GetString(
  [System.Convert]::FromBase64String($base64Password)
)

# 2. Log in (use --insecure to skip cert checks on localhost)
argocd login localhost:8080 `
  --username admin `
  --password $initialPassword `
  --insecure
```

### Deploy the app-of-apps

``` powershell
kubectl apply -f apps/app-of-apps.yaml
```
