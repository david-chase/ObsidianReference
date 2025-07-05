#mig #nvidia #gpu #k8s 

MIG Manager is designed as a controller within Kubernetes. It watches for changes to the `nvidia.com/mig.config` label on the node and then applies the user-requested MIG configuration When the label changes, MIG Manager first stops all GPU pods, including device plugin, GPU feature discovery, and DCGM exporter. MIG Manager then stops all host GPU clients listed in the `clients.yaml` config map if drivers are preinstalled. Finally, it applies the MIG reconfiguration and restarts the GPU pods and possibly, host GPU clients. The MIG reconfiguration can also involve rebooting a node if a reboot is required to enable MIG mode.

Q: Does MIG Manager allow you to dynamically slice GPU resources at runtime?
###### ChatGPT said:

Yes—but with important **caveats**. The **NVIDIA MIG Manager** _can_ dynamically slice GPU resources into MIG instances at runtime, but **there are constraints** that limit how truly “dynamic” this process is in practice.

---

### ✅ What MIG Manager _can_ do:

- **Automatically configure MIG profiles** on supported GPUs (A100, H100, L40S, etc.).
- **Apply desired MIG layout** on boot or via reconciliation (like a controller).
- Detect if the current MIG config **differs from the desired state**, and reconfigure it.
- You can define MIG configurations declaratively in a **ConfigMap** (via the GPU Operator).

---

### 🚫 What it _can't_ do (or shouldn't do easily):

- **Live-resize** a MIG instance (e.g., switch from `1g.5gb` to `3g.20gb`) while it's in use.
- **Evict and reshuffle MIG workloads on the fly** without disruption.

> MIG changes require the GPU to be in an **idle state**, with **no active processes**, and can briefly disrupt workloads on that GPU.

---

### ⚠️ Real-World Limitations

- MIG instance changes may require:
    - **Draining GPU workloads**
    - **Deleting existing MIG instances**
    - **Rebooting or resetting the GPU device**
- The GPU Operator does **not** continuously "autoscale" MIG layouts on demand (though you can script or orchestrate it).

---

### 🔧 Example Workflow (GPU Operator + MIG Manager):

1. Deploy the **GPU Operator** with MIG Manager enabled.
2. Create a ConfigMap that specifies the MIG layout per GPU.
3. The operator configures the GPUs accordingly.
4. Later, you can update the ConfigMap and reapply—**but you must ensure the GPUs are idle**.

---

### 🧠 Summary

| Capability                               | Supported by MIG Manager |
| ---------------------------------------- | ------------------------ |
| Initial MIG config on node start         | ✅ Yes                    |
| Reconciliation on ConfigMap change       | ✅ Yes                    |
| Autoscaling MIG profiles based on demand | ❌ No                     |
| Runtime slicing without disruption       | ⚠️ Only if GPU is idle   |