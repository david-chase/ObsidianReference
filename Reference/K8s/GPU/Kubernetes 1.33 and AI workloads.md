#k8s #ai

Kubernetes 1.33 (codename _Octarine_, released April 23, 2025) brings several key updates to better support AI/ML and HPC batch workloads—particularly enhancements to Jobs, scheduling, and GPU/device handling:

---

### 1. ✅ **SuccessPolicy for Indexed Jobs is GA**

- You can now define `.spec.successPolicy` in indexed Jobs to specify what constitutes success. This could be a minimum number of successful pods or success of specific indexes (e.g., the "leader" in MPI or distributed training).
    
- Once the policy is met, the Job is marked as complete and remaining pods are cleaned up, saving resources and reducing wait times [thenewstack.io+14kubernetes.io+14nops.io+14](https://kubernetes.io/blog/2025/05/15/kubernetes-1-33-jobs-success-policy-goes-ga/?utm_source=chatgpt.com).
    

---

### 2. ✅ **BackoffLimitPerIndex (Per-Index Retry Limits)**

- Each index in an indexed Job can now have its own retry limit (`backoffLimitPerIndex`). Failures in one shard don’t stop others from running—which is crucial for resilient, large-scale parallel workloads [medium.com+1linkedin.com+1](https://medium.com/google-cloud/upgrades-everything-new-with-kubernetes-1-33-2daead6b9fb4?utm_source=chatgpt.com).
    

---

### 3. 🎯 **Dynamic Resource Allocation (DRA) Enters Beta**

- DRA is now in beta, providing native support via `ResourceClaim` for requesting accelerators like GPUs, FPGAs, etc.
    
- This simplifies integration of AI/ML workloads without relying on out-of-tree plugins [linkedin.com+10nops.io+10komodor.com+10](https://www.nops.io/blog/whats-new-in-kubernetes-1-33-octarine-ai-ml-security-enhancements-more/?utm_source=chatgpt.com)[komodor.com+1loft.sh+1](https://komodor.com/blog/kubernetes-v133-an-insiders-perspective/?utm_source=chatgpt.com).
    

---

### 4. **NUMA-Aware Scheduling (Beta)**

- Scheduler improvements consider NUMA topology, reducing latency and improving throughput for AI/HPC tasks that benefit from locality .
    

---

### 5. **Asynchronous Preemption (Beta)**

- Scheduler can now preempt lower‑priority pods asynchronously. This enhances cluster responsiveness under AI/ML job pressures [dedirock.com+10nops.io+10komodor.com+10](https://www.nops.io/blog/whats-new-in-kubernetes-1-33-octarine-ai-ml-security-enhancements-more/?utm_source=chatgpt.com).
    

---

### 6. **Sidecar Containers Graduate to Stable**

- Native sidecar support ensures predictable start/stop behavior for service mesh, logging, or GPU initialization sidecars—common in ML pipelines .
    

---

### 🧩 **Why It Matters for AI Workloads**

|Feature|Benefit for AI/HPC|
|---|---|
|SuccessPolicy & BackoffLimitPerIndex|Handle leader-follower patterns and tolerate partial failures|
|DRA, NUMA scheduling, async preemption|Native and efficient hardware allocation|
|Stable sidecars|Reliable init/cleanup for GPU or logging workflows|

---

Overall, version 1.33 significantly enhances Kubernetes’ ability to run large‑scale, professional-grade AI, ML, and HPC training jobs with greater efficiency, fault tolerance, and resource utilization.