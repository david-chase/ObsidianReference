#requests #limits #eviction #scheduler 

##### You said:

Imagine I have a pod that requests 8Gi of memory and memory limits are 32Gi. As memory usage grows the free memory on the node shrinks, meaning the pod may end up being evicted due to memory pressure. However when the scheduler goes to place it, the only memory criteria it uses is that the node must have at least 8Gi of allocatable memory. So in theory the pod could be rescheduled on a node that has even less memory available for it than the one it was evicted from. Is this accurate?

###### ChatGPT said:

Short answer: **yes — that’s basically accurate**, with a couple of important nuances.
Let’s walk it through carefully, because your intuition is solid.

---

## What the scheduler _actually_ considers

When the Kubernetes scheduler places a pod, **it only considers resource _requests_**:

- Memory request = **8 Gi**
- Memory limit = **32 Gi** → **ignored by the scheduler**

So the scheduler’s hard rule is:

> “Can this node satisfy the _requested_ memory (8 Gi) based on allocatable memory and existing requests?”

It does **not** look at:

- Current _usage_
- Historical usage
- Memory _limits_
- Node memory pressure signals

---

## What triggers eviction

Evictions happen later, **on the node**, driven by the kubelet:

- As memory usage grows, **node free memory drops**
- Kubelet detects memory pressure (via eviction thresholds)
- It evicts pods based on:
    - QoS class (BestEffort → Burstable → Guaranteed)
    - Pod priority
    - Actual memory usage at that moment

So your pod can absolutely be evicted **because it’s using more than its request**, even though it’s still within its limit.

---

## The “rescheduled onto a worse node” scenario

This is the key part of your question, and yes — this can happen:

1. Pod is running on Node A
2. Node A enters memory pressure
3. Pod gets evicted
4. Scheduler looks for a new node
5. Node B:
    - Has ≥ 8 Gi _allocatable_ (by requests)
    - But **less actual free memory than Node A had at eviction time**
6. Scheduler happily places the pod on Node B
7. Pod grows again → eviction loop

This is **not a theoretical edge case** — it’s a well-known behavior in Kubernetes.

---

## Why Kubernetes works this way (by design)

Kubernetes separates responsibilities very intentionally:

- **Scheduler**: _static admission control_ (requests only)
- **Kubelet**: _runtime safety_ (actual usage & pressure)

The scheduler **cannot rely on instantaneous usage** because:

- Usage is volatile
- It would make scheduling non-deterministic
- It would cause thrashing under load

So Kubernetes assumes:

> “If you request too little, you accept the risk of eviction.”

---

## How QoS class factors in

Your pod:

- request ≠ limit → **Burstable QoS**

Burstable pods:

- Are **evicted before Guaranteed**
- Are allowed to grow beyond request
- Are _expected_ to be evicted under pressure

If you instead set:

`requests:   memory: 32Gi limits:   memory: 32Gi`

Then:

- Scheduler requires a node with 32 Gi free _by requests_
- Pod becomes **Guaranteed**
- It’s far less likely to be evicted (though still not impossible)

---

## The uncomfortable truth (which you’re circling)

Yes — Kubernetes can absolutely:

- Evict a pod due to memory pressure
- Then reschedule it onto a node that is _objectively worse_
- Repeat this indefinitely

That’s why:

- Under-requesting memory is dangerous
- Memory overcommit is a tradeoff, not free
- Stable workloads often use **request ≈ real usage**, not “optimistic” requests

---

## TL;DR

✔ **Your understanding is correct**  
✔ Scheduler only considers **requests**, not limits or usage  
✔ Eviction is based on **actual usage and node pressure**  
✔ A pod can be rescheduled onto a node with _less real memory available_  
✔ This behavior is **intentional**, not a bug