#ai #gpu #k8s #product 

https://project-hami.io/

**Project HAMi** (Heterogeneous AI Computing Virtualization Middleware) is an **open-source CNCF Sandbox project** designed to optimize and virtualize heterogeneous AI devices—like GPUs, NPUs, and more—within Kubernetes clusters. It enables **fine-grained device sharing**, **resource isolation**, and intelligent scheduling across multiple device types without requiring modifications to user applications.

---

### 🔑 **Key Features**

- **Device Virtualization & Sharing**
    - Virtual GPU (vGPU) slices with custom memory and compute limits
    - Dynamic MIG support on NVIDIA A100 and other devices 

- **Kubernetes-Native API**
    - Extends standard APIs (`nvidia.com/gpu`, `nvidia.com/gpumem`, `gpucores`)
    - Works with tools like Helm, Volcano, Koordinator, and KAI-scheduler 

- **Middleware Architecture**
    - Uses LD_PRELOAD to intercept CUDA calls and enforce quotas
    - Monitors GPU utilization via NVML and adjusts resource tokens regularly 

- **Multi-Vendor & Multi-Device Support**
    - Compatible with NVIDIA, Cambricon, Intel, AMD, Huawei Ascend, and others 

- **Observability**
    - Provides metrics via Prometheus and Grafana dashboards 

---

### ⚙️ **Architecture Components**

- **Device Plugin**: Registers vGPU resources with Kubernetes
- **Scheduler Extender**: Implements topology-aware scheduling logic
- **Mutating Webhook**: Injects necessary configs into Pods
- **HAMi‑Core**: Intercepts CUDA calls for memory & performance control [

---

### 🚀 **Use Cases & Adoption**

- Enables multiple lightweight inference tasks on a single GPU, significantly boosting utilization 
- Supports R&D workloads (e.g., Kubeflow notebooks), enabling fractional GPU use without full MIG
- Cloud providers can monetize GPU slices—for example, offering 3GB slices from H800 cards at competitive prices
- Industrial users like SNOW (South Korea) deploying mixed training A100 workloads on Kubernetes