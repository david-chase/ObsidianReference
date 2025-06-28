
# Argo CD Repository Structure for Helm, Kustomize, and YAML Across Multiple Clusters

This guide outlines best practices for organizing a Git repository to support clean, scalable deployments with Argo CD using Helm charts, Kustomize overlays, and raw YAML manifests — across multiple Kubernetes clusters.

---

## 🗂 Recommended Directory Structure

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

---

## ✅ Values Handling with Helm

You should:
- Leave the downloaded Helm chart untouched (`chart/`)
- Use separate `values.yaml` files (e.g. `tokyo-values.yaml`) to override only the settings you want to change
- All default values from the original `values.yaml` in the chart still apply unless explicitly overridden

Helm recursively merges the values from the base and override files.

---

## ✅ Example Argo CD Application with Layered Values

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

---

## ✅ Multi-Environment Layering

You can layer multiple `values.yaml` files:

```yaml
helm:
  valueFiles:
    - base-values.yaml
    - dev-values.yaml   # or prod-values.yaml
```

Later files override earlier ones.

---

## ✅ Mixing Helm, Kustomize, and YAML

Use subfolders like:

- `helm/` for charts and overrides
- `kustomize/` for Kustomize bases and overlays
- `manifests/` for raw Kubernetes YAML

---

## ✅ App-of-Apps Pattern (Optional)

Use a `root-app.yaml` to deploy all apps per cluster:

```yaml
spec:
  source:
    path: apps/clusters/tokyo
```

Argo CD will pick up each application file within that folder.

---

This structure supports scaling to many clusters, avoids chart mutations, and keeps the GitOps repo clean and predictable.
