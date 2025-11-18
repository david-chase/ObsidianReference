#mig #k8s #nvidia #gpu 

```table-of-contents
```
## Enabling MIG support

- Requires the NVidia GPU operator
- Specifically the NVidia Device Plugin 
- Provisioning is done by `nvidia-mig-parted`
- You create the mig configs you want in a ConfigMap, then you create a node label to determine which MIG config is applied to that node.

``` yaml
version: v1  
mig-configs:  
all-disabled:  
- devices: all  
mig-enabled: false  
  
all-enabled:  
- devices: all  
mig-enabled: true  
mig-devices: {}  
  
all-1g.5gb:  
- devices: all  
mig-enabled: true  
mig-devices:  
"1g.5gb": 7  
  
all-2g.10gb:  
- devices: all  
mig-enabled: true  
mig-devices:  
"2g.10gb": 3  
  
all-3g.20gb:  
- devices: all  
mig-enabled: true  
mig-devices:  
"3g.20gb": 2
```

- If a pod requests a specific mig config and there is no node labelled with that mig config, then the pod will go unscheduled

## Reconfiguring MIGs on an existing node

### MIG reconfiguration requires destroying the existing GPU instances

When you change a GPU’s MIG layout (e.g., from 1g.24gb × 4 → 2g.48gb × 2):

- ALL existing MIG instances on that GPU must be removed.
- Removing a MIG instance unbinds and resets the GPU partition.
- Any Kubernetes Pod using those MIG devices loses its device handle → it crashes or is evicted automatically.

**Therefore, all workloads on that GPU must restart.**

---
### What About Other GPUs in the Same Node?

Each GPU’s MIG configuration is independent.

- Reconfiguring **GPU0** does _not_ affect workloads on **GPU1**.
- Only workloads using the specific GPU whose MIG layout you modify are impacted.

---
### What About CPU-only workloads?

CPU-only Pods are unaffected **unless you choose to drain the node**.