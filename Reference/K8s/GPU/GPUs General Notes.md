#gpus #k8s 

```table-of-contents
```

## Install grove-system

``` powershell
helm upgrade --install grove oci://ghcr.io/ai-dynamo/grove/grove-charts:v0.1.0-alpha.3 -n grove-system --create-namespace
```

---
## GPU market share by vendor

| **Vendor** | **Estimated Enterprise GPU Market Share** | **Details / Notes**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ---------- | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **NVIDIA** | ~90 – 92 %                                | Dominates both data center and discrete markets. Jon Peddie Research reports ~92% share of AIB (Add‑In Board) GPUs in Q1 2025 [marketsandmarkets.com+9techspot.com+9tomshardware.com+9](https://www.techspot.com/news/108225-nvidia-reaches-historic-92-gpu-market-share-leaves.html?utm_source=chatgpt.com)[club386.com+1pcworld.com+1](https://www.club386.com/nvidia-enjoys-greater-dominance-in-gpu-market-against-amd-while-intel-disappears/?utm_source=chatgpt.com). Business Insider notes that NVIDIA held ~90% of the AI data-center GPU market in 2024 [time.com](https://time.com/7200909/ceo-of-the-year-2024-lisa-su/?utm_source=chatgpt.com). |
| **AMD**    | ~8 – 12 %                                 | Pushed down to ~8% in desktop discrete GPUs Q1 2025 . Some reports suggest AMD holds ~10–12% of broader enterprise/data-center share [marketwatch.com+11thetimes.co.uk+11techpowerup.com+11](https://www.thetimes.co.uk/article/advanced-micro-devices-takes-fight-to-nvidia-with-5bn-deal-7qh0zpbct?utm_source=chatgpt.com).                                                                                                                                                                                                                                                                                                                                |
| **Intel**  | <1 % (negligible)                         | Essentially pushed out of the discrete GPU market—Intel's share is under 0.1% for AIBs . It still participates in integrated and server GPUs, but market share remains minimal.                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

---

## Fractional GPU Instances
### Overview

Only one major cloud provider currently exposes fractional GPU resources directly at the virtual machine (instance) level: AWS. The other major clouds support GPU partitioning only inside container or Kubernetes environments (using NVIDIA MIG), not in VM SKUs. Below is a cloud-by-cloud summary.

### AWS

AWS offers true fractional GPU virtual machine types within the G6f family.

**Instance family:** G6f  
**GPU hardware:** NVIDIA L4, partitioned by the AWS hypervisor  
**Granularities:** 1/8, 1/4, 1/2, or full L4 GPU  
**Primary use cases:**

- High-density inference
- Small and mid-sized model serving
- Media processing and video transcoding
- Multi-tenant GPU workloads
- Cost-efficient GPU utilization

AWS is the only cloud where fractional GPUs are provisioned as VM instance types rather than being configured inside Kubernetes via MIG.

### Azure

Azure does not offer fractional GPU VM SKUs. It provides full-GPU VM families (NC, ND, NV) and supports MIG only inside AKS clusters on A100/H100 hardware.

**Instance family for fractional support:** None  
**Fractional GPU availability:** Only via MIG when running containers; not available at VM SKU level  
**Primary use cases when using MIG:**

- Partitioned GPU workloads inside AKS
- Not an option for fractional GPU VMs

### Google Cloud Platform (GCP)

GCP does not offer fractional GPU VM instance types. GCP supports full GPUs and allows MIG profiles only inside GKE clusters.

**Instance family for fractional support:** None  
**Fractional GPU availability:** Only via MIG in GKE  
**Primary use cases when using MIG:**

- Pod-level GPU slicing
- No direct VM-level fractional GPU consumption

### Oracle Cloud Infrastructure (OCI)

OCI provides full GPU shapes for A10, A100, and H100 hardware. MIG is supported inside OKE clusters but not at the VM SKU level.

**Instance family for fractional support:** None  
**Fractional GPU availability:** MIG only inside Kubernetes, not at VM level  
**Primary use cases when using MIG:**

- Containerized, partitioned GPU workloads
- No fractional GPU VMs

### Summary of Cloud Support for Fractional GPU VM Types

| Cloud | Fractional GPU VM Instance Types | Instance Families | Notes                               |
| ----- | -------------------------------- | ----------------- | ----------------------------------- |
| AWS   | Yes                              | G6f               | Hypervisor-level fractional L4 GPUs |
| Azure | No                               | None              | MIG only inside AKS                 |
| GCP   | No                               | None              | MIG only inside GKE                 |
| OCI   | No                               | None              | MIG only inside OKE                 |

Only AWS supports fractional GPU VM provisioning.

---

## Suitability of AWS Fractional GPU Instances for EKS Nodes

### When G6f Fractional GPU Instances Are a Good Fit for EKS

G6f instances are well suited to Kubernetes clusters that run many small or medium GPU workloads. They work particularly well for inference-focused clusters.

**Strong use cases include:**

- High-density inference where multiple small models run concurrently
- Multi-tenant GPU model serving
- Workloads where GPU memory rather than GPU compute is the limiting factor
- Clusters using model servers such as Triton, KServe, BentoML, or Ray Serve
- Autoscaling environments where pods request fractional GPU resources
- Cost-sensitive workloads that benefit from right-sizing GPU allocations

Fractional GPUs on G6f provide hardware-isolated slices with predictable performance characteristics, making them easier to operate and schedule than sharing a single full GPU among many pods.

### When G6f Fractional GPU Instances Are Not a Good Fit for EKS

Some workloads require full GPU capability, making fractional GPUs unsuitable.

**Use cases where G6f is not recommended:**

- Large-scale model training
- Distributed training using NCCL
- Very large model inference requiring full GPU memory or bandwidth
- Real-time latency-critical workloads requiring full GPU performance

In these scenarios, full-GPU families (G5, G6, P4d, P5, etc.) or specialized accelerators are more appropriate.

### Operational Considerations for G6f with EKS

AWS fully supports fractional GPUs on EKS with standard tooling.

**Supported components include:**

- NVIDIA Kubernetes Device Plugin
- Managed Node Groups and Karpenter
- Bottlerocket GPU AMIs
- Ubuntu GPU AMIs
- Scheduling using fractional GPU requests such as `nvidia.com/gpu: 0.25`

AWS exposes fractional GPU devices as extended resources, simplifying pod specifications and enabling predictable scheduling behavior.

### Final Assessment

AWS fractional GPU instances are an effective and efficient choice for EKS nodes running inference-heavy or multi-model GPU workloads. They are not suitable for training or workloads requiring full GPU compute or memory.

---

## NVIDIA MIG and Kubernetes DRA - Better together

Kubernetes **Dynamic Resource Allocation (DRA)** and **NVIDIA’s Multi-Instance GPU (MIG)** are **complementary**, not replacements.  
They solve **different layers** of the GPU resource-sharing problem — and when combined, they give you much finer control and better utilization.

### Layered Comparison

|Layer|Component|What It Does|Analogy|DRA Relationship|
|---|---|---|---|---|
|**Hardware partitioning**|**NVIDIA MIG**|Splits a single physical GPU (e.g. an A100 or H100) into multiple isolated GPU “instances” (e.g. 1g.10gb, 2g.20gb). Each behaves like a small, independent GPU.|Like dividing a physical CPU into hardware “cores” with dedicated VRAM.|DRA can _request and schedule_ these MIG instances, but doesn’t replace them.|
|**Kubernetes orchestration layer**|**DRA (Dynamic Resource Allocation)**|Defines a **standard Kubernetes API** (CRDs like `ResourceClaim` and `ResourceClass`) for **dynamic, pluggable resource allocation** beyond CPU/memory — e.g. GPUs, FPGAs, SmartNICs.|Like a broker between Pods and device drivers.|Works _on top of_ or _alongside_ MIG — DRA plugins can expose MIG instances as allocatable resources.|
|**Vendor integration layer**|**NVIDIA GPU Operator / DRA Plugin**|Implements the DRA API for NVIDIA GPUs, including MIG awareness, time-slicing, and device plugin lifecycle.|The “translator” between Kubernetes and GPU hardware.|Required for DRA to talk to MIG-capable devices.|

### How They Work Together

When used together in a modern Kubernetes setup (like your `tokyo` cluster with NVIDIA GPU Operator):

1. **GPU Operator enables MIG** on the GPU nodes (`miami` and `obsidian`).
2. MIG partitions the GPU into several **MIG devices** (e.g., `nvidia.com/mig-1g.10gb`).
3. The **NVIDIA DRA plugin** registers each MIG instance as an allocatable resource.
4. When a Pod requests GPU via a **`ResourceClaim`**, the DRA framework dynamically assigns a MIG instance that matches the Pod’s needs.
5. Kubernetes handles scheduling and lifecycle via DRA — **without static device plugin allocations**.

This combination yields:

- **Fine-grained GPU slicing** (via MIG)
- **Dynamic allocation & cleanup** (via DRA)
- **Better isolation & utilization** (no more static GPU binding)
- **Future-proof extensibility** (same DRA model can apply to NICs, FPGAs, storage, etc.)

### Without DRA

- You’d rely on static device plugin allocation (`nvidia.com/gpu: 1`).
- MIG devices could still be used, but **you must predefine how many MIG slices exist**, and Kubernetes can’t dynamically create or reallocate them.
- No central API to claim/release GPU slices at runtime.

### Without MIG

- DRA can still allocate whole GPUs or virtual/time-sliced GPUs (using NVIDIA’s `time-slicing` mode).
- You gain flexibility in scheduling, but lose **hardware-level isolation** and predictable QoS.

### Summary

| Aspect          | MIG                     | DRA                                 |
| --------------- | ----------------------- | ----------------------------------- |
| **Purpose**     | Hardware partitioning   | Dynamic allocation & orchestration  |
| **Scope**       | NVIDIA GPUs only        | Any device (generic framework)      |
| **Isolation**   | Strong (hardware-level) | Depends on underlying device/plugin |
| **Flexibility** | Fixed slices            | Dynamic claims/releases             |
| **Relation**    | Hardware foundation     | Orchestration layer built on top    |

### TL;DR

> ✅ **MIG = how GPUs are sliced**  
> ✅ **DRA = how Kubernetes assigns those slices to Pods**  
> 🔄 **They’re designed to work together**, not replace one another.


---

## Poor GPU utilization contributing factors

- Using vanilla Kubernetes scheduler that doesn't support fractional GPUs
	- No gang scheduling (related pods are scheduled together or not at all)
	- No fractional GPUs
	- Dynamic resource reclamation
- No GPU sharing strategy in place
- Pods being scheduled on GPU instances that require no GPU
- Workloads being scheduled on GPU nodes that are part of a long pipeline, and only a small part of that pipeline needs GPU
- MIG config is incorrect, pods requesting mig configs that don't exist

---

## Training vs inference hardware requirements

### Training vs. Inference: Hardware Considerations

|Feature|Training|Inference|
|---|---|---|
|**Purpose**|Learn patterns from large datasets|Apply learned patterns to new data|
|**Hardware Needs**|High compute, high memory bandwidth, large batch sizes|Low latency, often lower power consumption|
|**Typical Devices**|High-end GPUs (e.g. NVIDIA A100, H100) or multi-GPU servers|Mid-range GPUs (e.g. T4, L4, A10), CPUs, edge accelerators|
|**Precision**|Often uses FP32 or mixed precision (FP16, BF16)|Often uses INT8 or FP16 for speed|
|**Batch Sizes**|Large (to maximize throughput)|Small or single-batch (to reduce latency)|
|**Duration**|Hours to days|Milliseconds per request|

### Why Not Use the Same Hardware?

- **Cost**: Training GPUs are much more expensive and overkill for inference.
- **Latency vs. Throughput**:
    - Training favors throughput (how much data can be processed over time).
    - Inference favors low latency (how fast you get a prediction).
- **Power and Heat**: Training burns more power; inference can run on smaller, cooler setups—even on mobile or edge devices.

---

## Standard Node Labels for GPU Nodes

|Label Key|Description|Example|
|---|---|---|
|`nvidia.com/gpu.present`|Automatically set to `"true"` by the [NVIDIA device plugin](https://github.com/NVIDIA/k8s-device-plugin) when a GPU is detected|`nvidia.com/gpu.present=true`|
|`nvidia.com/gpu.product`|Indicates the GPU model (used by NVIDIA GPU Operator)|`nvidia.com/gpu.product=Tesla-T4`|
|`nvidia.com/gpu.count`|Number of GPUs on the node (optional custom)|`nvidia.com/gpu.count=1`|
