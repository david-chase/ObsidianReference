#k8s #nodes #hardware 

**Node-declared features** are **capabilities or attributes that a Kubernetes node advertises about itself**—such as hardware, kernel, or software characteristics. These features help the **scheduler** and **workload controllers** make informed decisions about where to place pods.

They represent “what the node can do,” not “what it’s currently doing.”

---
### 🧩 **Key Concepts**

- **Feature Sources:**  
    Features can come from multiple layers:
    - **Kubelet (built-in):** CPU architecture, OS, hostname, runtime version.
    - **Node Feature Discovery (NFD):** Hardware and software traits discovered automatically.
    - **Custom Labels / Annotations:** User-defined metadata for scheduling.
- **Feature Representation:**  
    Features are usually exposed as **node labels**, e.g.:
    `kubectl get node havana -o jsonpath='{.metadata.labels}'`

---
### 🧠 **Examples of Node-Declared Features**

| Category              | Example Label                                                      | Meaning                        |
| --------------------- | ------------------------------------------------------------------ | ------------------------------ |
| **CPU**               | `feature.node.kubernetes.io/cpu-hardware_multithreading=true`      | CPU supports hyper-threading   |
| **Architecture**      | `kubernetes.io/arch=amd64`                                         | Node runs x86_64 architecture  |
| **GPU / Accelerator** | `feature.node.kubernetes.io/pci-10de.present=true`                 | NVIDIA GPU detected            |
| **Kernel / OS**       | `feature.node.kubernetes.io/kernel-version.full=6.14.0-33-generic` | Kernel version                 |
| **NUMA topology**     | `feature.node.kubernetes.io/memory-numa=2`                         | Two NUMA zones present         |
| **Security**          | `feature.node.kubernetes.io/selinux=true`                          | SELinux enabled                |
| **Cloud / Zone**      | `topology.kubernetes.io/zone=us-east-1a`                           | Cloud zone location            |
| **Custom Feature**    | `custom.node.kubernetes.io/disk-ssd=true`                          | User-added label for SSD nodes |

---
### 🧰 **Node Feature Discovery (NFD)**

NFD is a **DaemonSet** that runs on every node to detect and advertise node features automatically.

**Example workflow:**

1. NFD scans `/proc/cpuinfo`, `/sys`, PCI, USB, etc.
2. It generates node labels (like those above).
3. The Kubernetes scheduler can then use those labels in **nodeSelectors**, **affinity rules**, or **taints/tolerations**.
    

Example deployment snippet:

``` yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nfd-worker
  namespace: node-feature-discovery
spec:
  template:
    spec:
      containers:
        - name: nfd-worker
          image: ghcr.io/kubernetes-sigs/node-feature-discovery:v0.15.3
```

---

### 🧩 **In Scheduling**

Workloads can target specific node features like so:

``` yaml
spec:   
	nodeSelector:     
		feature.node.kubernetes.io/pci-10de.present: "true"
```

Or more flexibly:

``` yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: feature.node.kubernetes.io/cpu-hardware_multithreading
                operator: In
                values: ["true"]
```

---

### 🔍 **Why It Matters**

- Enables **hardware-aware scheduling** (e.g., GPU, FPGA, NUMA, CPU flags).
- Supports **fine-grained workload placement** and **performance tuning**.
- Foundation for **Dynamic Resource Allocation (DRA)** and **topology-aware** scheduling.
- Used by many **AI, HPC, and edge computing** workloads to match pods to capable nodes.