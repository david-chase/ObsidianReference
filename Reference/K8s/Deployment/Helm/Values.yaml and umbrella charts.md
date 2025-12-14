```table-of-contents
```
## Summary

This document summarizes how Helm umbrella charts can control configuration values for nested subcharts using a single top-level `values.yaml` file.

The core concept is that **Helm values flow downward** through the dependency tree. A parent (umbrella) chart can override values for its direct dependencies, and for those dependencies’ own subcharts, by following the **chart name hierarchy** exactly as defined in each chart’s `Chart.yaml`.

---

## Scenario

Dependency structure:

```
umbrella-chart
└─ prometheus (prometheus-community/prometheus)
   └─ prometheus-node-exporter (prometheus-community/prometheus-node-exporter)
```

Each chart has its own `values.yaml`, but all values can be overridden from the umbrella chart.

---

## How Value Inheritance Works

- Helm merges values from parent to child
- Subchart defaults are loaded first
- Parent chart values override subchart defaults
- CLI flags (`--set`, `--values`) override everything

**Important rule:**  
You must use the **exact chart name** from `Chart.yaml`, not the repo name or directory name.

---

## Example: Setting a nodeSelector for node-exporter

To apply a `nodeSelector` to the **node-exporter DaemonSet** from the umbrella chart, add the following to the umbrella chart’s `values.yaml`:

```yaml
prometheus:
  prometheus-node-exporter:
    nodeSelector:
      kubernetes.io/os: linux
      node-role.kubernetes.io/worker: "true"
```

This value is merged as:

```
umbrella → prometheus → prometheus-node-exporter
```

and is rendered into the node-exporter manifest during `helm install` or `helm template`.

---

## Why This Works

The Prometheus chart exposes node-exporter configuration under:

```yaml
prometheus-node-exporter:
  enabled: true
  nodeSelector: {}
```

The umbrella chart simply overrides this block.

---

## Common Gotchas

1. **Chart name matters**
   - Always use the `name:` field from `Chart.yaml`

2. **Not all charts expose subcharts the same way**
   - Some charts wrap subchart values differently
   - Always inspect the parent chart’s `values.yaml`

3. **Works with Argo CD**
   - Argo CD passes Helm values through unchanged
   - This pattern works cleanly with GitOps workflows

---

## Verification Tip

To confirm your values are applied correctly:

```powershell
helm template ./umbrella-chart --debug |
  Select-String nodeSelector -Context 3,3
```

If the selector appears in the node-exporter DaemonSet, the override is working.

---

## Key Takeaway

Helm umbrella charts provide full control over deeply nested subcharts through structured value overrides, without forking charts or using post-renderers.
