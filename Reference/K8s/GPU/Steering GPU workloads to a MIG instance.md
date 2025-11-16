#mig #nvidia #k8s #gpu 

```table-of-contents
```
# **Introduction**

To steer a workload onto a specific **MIG instance**, you don’t schedule directly to the MIG “slice” itself — you schedule to the **resource type** that the GPU Operator exposes once MIG is enabled.

Here’s the key idea:

### **Each MIG slice type becomes a Kubernetes resource**

After enabling MIG on your H100 nodes (like in the article), the NVIDIA GPU Operator exposes MIG slices as _distinct allocatable resources_ on the node, such as:

- `nvidia.com/mig-1g.10gb`
- `nvidia.com/mig-2g.20gb`
- `nvidia.com/mig-3g.40gb`
- `nvidia.com/mig-1g.24gb` (H100-specific sizes)
- etc.

You **request** one of these resources in your Pod spec, and the scheduler will place your workload onto a node where that exact MIG instance exists and is free.

---

# **How to steer to a specific MIG instance**

You don’t name the instance directly — you request the **profile** (e.g., `1g.24gb`).  
Example:

`apiVersion: v1 kind: Pod metadata:   name: my-mig-workload spec:   containers:   - name: trainer     image: pytorch/pytorch:latest     resources:       limits:         nvidia.com/mig-1g.24gb: 1`

This tells Kubernetes:

> “Schedule this Pod on a node where a free **1g.24gb** MIG slice exists.”

The NVIDIA GPU Operator and the device plugin handle the mapping to the _actual_ MIG device (e.g., `GPU0/gi0/ci0`).

---

# **If you want to steer to a specific physical GPU (not just the slice type)**

Then you add **nodeSelector / nodeAffinity**.

Example: force the pod to run only on node _obsidian_:

`spec:   nodeSelector:     kubernetes.io/hostname: obsidian   containers:   - name: trainer     resources:       limits:         nvidia.com/mig-1g.24gb: 1`

This still doesn’t target a specific MIG instance — but it ensures you use a MIG slice on a particular GPU host.

---

# **If you truly want to bind a job to a _specific_ MIG instance ID**

You must use the **NVIDIA GPU Feature Discovery (GFD) extended labels**.

The GPU Operator automatically labels nodes like:

`nvidia.com/mig.config=all-disabled nvidia.com/mig.strategy=single nvidia.com/mig-1g.24gb.count=2 nvidia.com/mig-1g.24gb.gpu0=1 nvidia.com/mig-1g.24gb.gpu1=1`

This lets you constrain scheduling **to a specific parent GPU**:

Example: schedule onto MIG slices on GPU 0 only:

`spec:   affinity:     nodeAffinity:       requiredDuringSchedulingIgnoredDuringExecution:         nodeSelectorTerms:         - matchExpressions:           - key: nvidia.com/mig-1g.24gb.gpu0             operator: Exists`

Still — Kubernetes doesn’t bind to `gi0/ci0` specifically, only to the slices on GPU0.

If you need _exact instance pinning_, that requires:

### **A: Using NVIDIA’s DRA (Device Resource Assignment)** — _the future_

### **B: Writing custom logic (not typical)**

---

# **Summary**

To use workloads with MIG instances:

### ✔️ **Request the MIG profile:**

`resources.limits: nvidia.com/mig-1g.24gb: 1`

### ✔️ (Optional) Steer to a specific node:

`nodeSelector: { kubernetes.io/hostname: obsidian }`

### ✔️ (Optional) Steer to a specific physical GPU:

Use node affinity for labels like `nvidia.com/mig-1g.24gb.gpu0`.