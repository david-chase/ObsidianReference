#product #gpu #k8s 

https://github.com/ppc64le-cloud/kubeshare

**KubeShare** is a GPU-sharing framework for Kubernetes that enables multiple pods to safely and efficiently share GPU resources. It is particularly useful in environments where GPU utilization is low or bursty (such as in inference workloads), allowing better resource usage by time-sharing access to GPUs. KubeShare was developed with a focus on _academic and research clusters_, but it is compatible with general Kubernetes setups.

---

### 🔧 Key Features

- **GPU Time-Sharing**: Enables multiple containers to share a single GPU device, coordinated by the KubeShare scheduler extension.
- **Custom Resource Definitions (CRDs)**: Defines a custom `SharePod` resource for managing GPU usage quotas and coordination.
- **Quota Enforcement**: Allocates and enforces GPU usage quotas per pod to ensure fair sharing.
- **Compatible with NVIDIA GPUs**: Designed to work with NVIDIA GPU environments and drivers.
- **Integration with Kubernetes Scheduler**: Provides a scheduler extender to augment the native Kubernetes scheduler for GPU-aware decisions.

---

### 📦 Architecture Components

- **KubeShare Scheduler Extender**: Augments Kubernetes scheduling decisions with GPU availability and quota data.
- **KubeShare Controller**: Monitors and manages GPU usage across nodes using CRDs.
- **Quota Device Plugin**: Reports GPU availability and enforces time-based sharing policies.