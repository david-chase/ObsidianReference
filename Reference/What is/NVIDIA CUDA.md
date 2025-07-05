#nvidia #gpu #definition 

**CUDA** (Compute Unified Device Architecture) is **NVIDIA’s parallel computing platform and programming model** that allows developers to harness the full power of **NVIDIA GPUs** for general-purpose computing (GPGPU).

---

### 🔧 What CUDA Enables

Traditionally, GPUs were used only for graphics. CUDA allows you to use the GPU for **general-purpose parallel workloads**—like AI, simulations, and big data processing.

---

### 🧠 Key Concepts

|Term|Description|
|---|---|
|**GPU Kernels**|Small functions written in CUDA C/C++ that run on the GPU, often in parallel.|
|**Threads**|CUDA lets you launch **thousands of threads** to do computations in parallel.|
|**Memory Hierarchy**|Shared, global, and device memory allow fast, parallel data access.|
|**Host vs. Device**|Your **CPU is the host**, and your **GPU is the device**. Code runs on both.|

Q: So CUDA isn't a reference to hardware, but rather to a programming framework that allows you access to the hardware?

###### ChatGPT said:

Exactly, Dave — **CUDA is a software framework**, not a specific piece of hardware.

---

### ✅ In Short:

> **CUDA = NVIDIA's software stack that lets you write code to run on their GPUs.**