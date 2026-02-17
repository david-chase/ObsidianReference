#mig #k8s #nvidia #gpu 

```table-of-contents
```

## MIG vs time slicing

| Feature                         | **MIG (Multi-Instance GPU)**                                           | **GPU Time Slicing**                                |
| ------------------------------- | ---------------------------------------------------------------------- | --------------------------------------------------- |
| **Type of sharing**             | **Hardware-based** partitioning                                        | **Software-based** time multiplexing                |
| **Isolation**                   | Strong (dedicated cores, memory, cache)                                | Weak (processes take turns on shared hardware)      |
| **Performance**                 | Predictable, consistent                                                | Variable (depends on scheduling fairness and load)  |
| **Available on**                | A100, H100, L40S, some newer GPUs                                      | Most NVIDIA GPUs                                    |
| **Number of workloads**         | Limited by MIG profiles (e.g., 7 per A100 max)                         | Higher concurrency possible, but with contention    |
| **K8s Support**                 | Built into GPU Operator and device plugin (`nvidia.com/mig-<profile>`) | Needs manual setup with NVIDIA time-slicing toolkit |
| **Latency-sensitive workloads** | ✅ Suitable                                                             | ⚠️ Often not ideal                                  |
| **Deployment use case**         | Production workloads needing isolation                                 | Dev/test, bursty jobs, lightweight inference        |

## Comparison of GPU Time Slicing Approaches

| **Method**                                             | **Mechanism**                                                                                                | **Use Case**                                               | **Pros**                                                                  | **Cons**                                                                   |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **NVIDIA GPU Time Slicing (Default Driver Scheduler)** | GPU driver schedules multiple CUDA contexts in round-robin fashion                                           | Lightweight concurrent workloads, dev/test environments    | ✅ Requires no special setup  <br>✅ Available on most NVIDIA GPUs          | ❌ Weak isolation  <br>❌ Performance can vary under load                    |
| **NVIDIA `nvidia-time-slicing` Toolkit**               | Container runtime plugin that enforces time slice boundaries (e.g., via cgroups and runtime class overrides) | Better-controlled containerized workloads in Kubernetes    | ✅ Lets you define fixed time slices per container  <br>✅ K8s integratable | ❌ Still shares physical resources  <br>❌ Requires NVIDIA container toolkit |
| **Kubernetes Device Plugin Multi-Container Sharing**   | GPU is shared across pods by modifying the device plugin behavior                                            | Running multiple pods on the same GPU                      | ✅ Transparent to workloads  <br>✅ Easy to prototype                       | ❌ Needs custom device plugin  <br>❌ No resource guarantees                 |
| **vGPU (NVIDIA GRID or vGPU Manager)**                 | Software virtualization of GPU at the hypervisor level (e.g., VMware, Citrix)                                | Multi-tenant VMs or VDI environments with virtualized GPUs | ✅ Stronger user isolation  <br>✅ Centralized control in VDI setups        | ❌ Commercial licensing required  <br>❌ Lower performance for AI workloads  |
| **MPS (Multi-Process Service)**                        | Shares GPU context between multiple CUDA processes                                                           | Single user with many lightweight CUDA jobs                | ✅ Reduced launch overhead  <br>✅ Better GPU utilization                   | ❌ Not container-aware  <br>❌ Limited K8s support                           |

### When to use different time slicing methods

| **Use this if...**                             | **Best Method**                 |
| ---------------------------------------------- | ------------------------------- |
| You're in Kubernetes and want soft GPU sharing | `nvidia-time-slicing` toolkit   |
| You're doing HPC-style CUDA job multiplexing   | NVIDIA MPS                      |
| You're running multi-user virtual desktops     | NVIDIA vGPU / GRID              |
| You want strong isolation and performance      | Use MIG instead of time slicing |

## How to spot GPU sharing

| Sharing strategy                                   | Signature                                                                     | Type                 | Comments                                                                                                              |
| -------------------------------------------------- | ----------------------------------------------------------------------------- | -------------------- | --------------------------------------------------------------------------------------------------------------------- |
| MPS                                                | `nvidia-cuda-mps-control`                                                     | daemonset            | Shares GPU context between multiple CUDA processes                                                                    |
| MPS                                                | `kubectl describe pod <pod-name> \| Select-String "CUDA_MPS"`                 | environment variable |                                                                                                                       |
| MIG                                                | `nvidia-mig-manager`                                                          | daemonset            |                                                                                                                       |
| NVIDIA GPU Time Slicing (Default Driver Scheduler) | n/a                                                                           |                      | If no other sharing tech is being used and multiple pods are sharing GPUs, you're probably using default time slicing |
| `nvidia-time-slicing` Toolkit                      | `kubectl get pod <pod-name> -o yaml \| Select-String "NVIDIA_TIME_SLICE"`<br> | environment variable |                                                                                                                       |
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

---
## Reconfiguring MIGs on an existing node

### MIG reconfiguration requires destroying the existing GPU instances

When you change a GPU’s MIG layout (e.g., from 1g.24gb × 4 → 2g.48gb × 2):

- ALL existing MIG instances on that GPU must be removed.
- Removing a MIG instance unbinds and resets the GPU partition.
- Any Kubernetes Pod using those MIG devices loses its device handle → it crashes or is evicted automatically.
- The node itself doesn't need to restart, only the GPUs where MIG config is being applied get reset.

**Therefore, all workloads on that GPU must restart.**

### What About Other GPUs in the Same Node?

Each GPU’s MIG configuration is independent.

- Reconfiguring **GPU0** does _not_ affect workloads on **GPU1**.
- Only workloads using the specific GPU whose MIG layout you modify are impacted.

### What About CPU-only workloads?

CPU-only Pods are unaffected **unless you choose to drain the node**.

---

## Enabling MIG support on CSP nodes

- By default EKS, AKS, and GKE nodes don't have MIG support enabled
- This has to be enabled before you can apply a MIG config
- Most public cloud instances need to be rebooted prior to applying a MIG config, unless it's on-prem or a bare-metal instance

