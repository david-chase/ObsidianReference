#nodes #k8s #autoscaling 

```table-of-contents
```
## Nodepools and EC2NodeClasses

When using **Karpenter v1.x** on **Amazon EKS**, you define:

- **One or more `NodePools`** ✅ _(required)_
- **One or more `EC2NodeClasses`** ✅ _(required on AWS)_

They work **together**, but they play very different roles.
### How they fit together

#### `NodePool` — _what_ nodes you want

This is the **policy / intent** layer.

A `NodePool` defines things like:

- Instance requirements (CPU, memory, architecture)
- Capacity type (on-demand vs spot)
- Labels & taints
- Consolidation and disruption rules
- Limits (e.g., max total CPU)

Think of it as:

> _“When Karpenter needs nodes for these pods, here’s what they should look like.”_

You’ll often have **multiple NodePools**:

- general workloads
- GPU workloads
- spot-only workloads
- latency-sensitive workloads

#### `EC2NodeClass` — _how_ nodes are created

This is the **AWS infrastructure** layer.

An `EC2NodeClass` defines:

- AMI family or explicit AMIs
- IAM role
- Subnets
- Security groups
- Block device mappings
- User data

Think of it as:

> _“Here’s how to actually build an EC2 instance.”_

Multiple NodePools can reference **the same EC2NodeClass**, or you can create multiple classes if the infra differs.

### The relationship (important nuance)

A **NodePool must reference exactly one EC2NodeClass**  
An **EC2NodeClass can be referenced by many NodePools**

Example mental model:

`NodePool (gpu-spot)  ─┐ NodePool (gpu-ondemand) ──► EC2NodeClass (gpu-al2) NodePool (batch)     ─┘`

---

### Minimum viable setup

At minimum, you need:

- 1 NodePool
- 1 EC2NodeClass
- NodePool → references EC2NodeClass

Without either one, Karpenter **cannot provision nodes**.

### Common real-world patterns

You’ll usually end up with:

- Few EC2NodeClasses (1–3)
- Many NodePools (policy-driven)

That keeps infra DRY and policy flexible — very much the Kubernetes way.

---
## Karpenter and pod limits

Does Karpenter consider pod limits when doing node consolidation?

Good question 👍

Yes — **Karpenter considers both pod requests and limits during node consolidation**, but in different ways:

- **Scheduling / bin-packing decisions** (including consolidation moves) are always made based on **requests**, not limits. That’s because requests are the guaranteed reservation the scheduler (and Karpenter) can rely on.
- However, **limits are not ignored** — Karpenter uses them in two situations:
    1. **Pod Density Constraints** – If `requests` are much lower than `limits`, Karpenter won’t try to cram so many pods together that they would exceed their `limits` if they all spiked at once. This comes from respecting Kubernetes’ `NodeResourcesFit` plugin logic.
    2. **Eviction Risk** – When evaluating consolidation, Karpenter checks if moving pods onto fewer nodes would push CPU or memory usage close to `limits`. If so, it may decline the consolidation because it could cause kubelet eviction under pressure.

In short:

- **Primary driver** = pod requests (that’s what consolidation math uses).
- **Safety check** = pod limits, to avoid creating unstable nodes.

Would you like me to show you how to inspect Karpenter’s scheduling/packing logs on your cluster, so you can actually see whether it’s considering limits in practice during a consolidation cycle?

### What is `NodeResourcesFit`?

It’s a **default scheduling plugin** in Kubernetes (part of the Scheduler framework).  
Its job is to decide whether a Pod can fit on a Node based on resource accounting.

### What it checks

`NodeResourcesFit` has two phases:

1. **Filter (hard pass/fail):**
    - Compares **sum of pod requests** (CPU, memory, ephemeral-storage, extended resources like GPUs) against what’s allocatable on the node.
    - If the Pod’s requests don’t fit, the node is filtered out immediately.
    
    👉 **This uses only requests, not limits.**
    
2. **Score (soft ranking):**
    
    - If multiple nodes can host the Pod, `NodeResourcesFit` can score nodes based on how well they would be “packed.”
    - There are different scoring strategies (e.g. `LeastAllocated`, `MostAllocated`, `BalancedAllocation`), and you can configure which one your scheduler uses.

### Where limits come in

While `NodeResourcesFit` itself doesn’t consider limits directly, limits come into play in two ways:

- **QoS class determination** (Guaranteed, Burstable, BestEffort), which impacts eviction priority when nodes are under pressure
- **Kubelet eviction**: If actual usage approaches limits (especially memory), pods may be killed even though they “fit” by requests.

This is why **Karpenter’s consolidation looks at requests (via `NodeResourcesFit`) but still does sanity checks against limits/usage** to avoid triggering evictions when collapsing nodes.

---

## Karpenter and MIG configs

If you want to deploy nodes with MIG configs, would this be part of your nodepool?

Short answer: **yes — _selection_ lives in the NodePool, _configuration_ does not.**
Here’s the precise split when using **Karpenter** with MIG.

### What goes in the **NodePool** (YES)

The **NodePool** is where you express **intent and scheduling constraints** for MIG-capable nodes.

You typically put **labels, taints, and requirements** here.

Example:

```
spec:
  template:
    metadata:
      labels:
        nvidia.com/mig.config: all-1g.10gb
    spec:
      taints:
        - key: nvidia.com/mig
          effect: NoSchedule
      requirements:
        - key: nvidia.com/gpu.product
          operator: In
          values: [ A100-SXM4-40GB ]
```

This does **not** configure MIG — it tells the scheduler:

> “These nodes will have this MIG profile.”

Pods then select this NodePool via:

- tolerations
- nodeAffinity
- labels

### What does **NOT** go in the NodePool (important)

The NodePool **does not actually enable or apply MIG**.

No MIG mode switching  
No GPU partitioning  
No device configuration

Karpenter **never touches the GPU driver stack**.

### Where MIG is actually configured (the real work)

#### ✅ **EC2NodeClass → userData**

This is where you:

- enable MIG mode
- apply a specific MIG layout
- reboot if required

Example (conceptual):

```
spec:
  userData: |
    nvidia-smi -mig 1
    nvidia-smi mig -cgi 0,0,0,0,0,0,0
```

This runs **before** Kubernetes sees the node.

#### ✅ **NVIDIA GPU Operator** (recommended)

In practice, you usually let **NVIDIA GPU Operator** handle this.

You configure MIG profiles via:

- ClusterPolicy
- node labels like `nvidia.com/mig.config`

Flow:

1. Karpenter provisions the node
2. NodePool labels it
3. GPU Operator sees the label
4. GPU Operator applies the MIG layout

This is the **cleanest and safest** design.

### Mental model (lock this in)

- **NodePool** → _“This node is intended for MIG profile X”_
- **EC2NodeClass / GPU Operator** → _“Make the GPU actually look like that”_
- **Pods** → request specific MIG resources (e.g. `nvidia.com/mig-1g.10gb`)

Karpenter never configures hardware — it only matches intent to supply.

### Best practice (what people get right vs wrong)

**Right**

- Separate NodePools per MIG profile
- Use taints to prevent accidental scheduling
- Let GPU Operator manage MIG state

**Wrong**

- Trying to configure MIG in NodePool
- Mixing MIG and non-MIG workloads in one pool
- Relying on pod resource requests alone