#jobs #scheduling #k8s #product #project 

https://kueue.sigs.k8s.io/

Kueue is a **Kubernetes-native job queueing and workload orchestration system** designed for high-performance, AI/ML, and batch workloads. It manages how jobs are _admitted_, _queued_, and _scheduled_ across clusters, especially when resources (CPU, GPU, memory, quotas) are scarce or shared between teams.  
Kueue does **not** replace the underlying job runners (e.g., Kubernetes Jobs, Ray, MPI, Volcano, Tekton). Instead, it **controls when jobs may start** based on fairness, quotas, and resource availability.

Think of it as:  
_“A workload admission controller for Kubernetes batch/AI jobs.”_

---

## Key capabilities

### Core features

- **Multi-tenant workload fairness**  
    Ensures teams or tenants receive fair access to shared cluster capacity.
- **Queueing & job admission**  
    Jobs wait in queues until resources are available, preventing over-scheduling.
- **Cluster-wide quota enforcement**  
    Uses “ClusterQueues” to allocate capacity across multiple namespaces.
- **Preemption support**  
    Can stop or requeue running jobs if higher-priority workloads need resources.
- **PodGroup support**  
    Supports workloads that require gang scheduling (all pods must start together) such as AI/ML training.
- **Compatible with many job frameworks**  
    Works with Kubernetes Job, JobSet, RayJob, VolcanoJob, MPIJob, etc.

---

## Architectural components

### 1. **LocalQueue**

- Lives in a namespace.
- Jobs submitted in that namespace enter a LocalQueue.
- Helps isolate team workloads.

### 2. **ClusterQueue**

- Aggregates capacity across node pools or the entire cluster.
- Defines
    - Resource quotas
    - Cohort membership (grouping queues that share capacity)
    - Priority and fairness policies

### 3. **Workload objects**

- Abstract representation of a job (Kueue converts Jobs/JobSets into Workloads).
- Kueue tracks Workloads until they are admitted and running.

### 4. **Admission controller**

- Decides _when_ a job is allowed to start.
- Only admits a job when required resources are available in the ClusterQueue.

---

## Typical use cases

- **AI/ML training queues**  
    Control access to limited GPU nodes.
- **HPC batch compute environments**
- **Cost-optimization / resource-sharing clusters**  
    Prevent “cluster flooding” when many jobs are submitted at once.
- **Multi-tenant Kubernetes platforms**  
    Enforce per-team quotas and priorities.

---

## How it differs from Kubernetes HPA/VPA or cluster autoscaling

- **HPA/VPA**: Adjust running workloads’ resource requests/replicas  
    (but do not delay job start).
- **Cluster autoscaler / Karpenter**: Adds/removes nodes  
    (but does not coordinate job admission).
- **Kueue**: Controls _when_ jobs begin executing  
    based on quotas and scheduling fairness.

These systems are often used **together**.

---

## Advantages

- Kubernetes-native CRDs; no external scheduler required.
- Predictable resource sharing between teams.
- Supports gang-scheduled ML workloads.
- Prevents job thrashing and overload.
- Pluggable with existing job frameworks—increments, not replaces.

---

## Limitations

- Focused on batch/AI jobs, **not** general purpose deployments.
- Requires operator installation and configuration (ClusterQueues, LocalQueues).
- Still maturing (introduced in SIG-Scheduling; not all schedulers are integrated).