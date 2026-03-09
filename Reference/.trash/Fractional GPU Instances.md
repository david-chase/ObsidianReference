#aws #gpu #nodes 

```table-of-contents
```

## Overview

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

## Summary of Cloud Support for Fractional GPU VM Types

|Cloud|Fractional GPU VM Instance Types|Instance Families|Notes|
|---|---|---|---|
|AWS|Yes|G6f|Hypervisor-level fractional L4 GPUs|
|Azure|No|None|MIG only inside AKS|
|GCP|No|None|MIG only inside GKE|
|OCI|No|None|MIG only inside OKE|

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