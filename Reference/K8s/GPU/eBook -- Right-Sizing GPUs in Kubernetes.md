#ebook #gpus 


## Section 2

- A GPU does not schedule threads one at a time. Instead, threads are grouped into warps, which are bundles of 32 threads that execute together. All 32 threads in a warp run the same instruction at the same time. Warps are assigned to Streaming Multiprocessors (SMs), which are the main compute units on the GPU.
- Each SM contains CUDA cores (where the math happens), shared memory, registers, and warp schedulers.
- When an SM has warps assigned and those warps are running instructions (not waiting for memory or synchronization), computation is happening. When SMs are empty or warps are stalled, the GPU is waiting.
- Together, these two metrics help you tell the difference between just launching kernels and the GPU having enough real work to stay busy. But they still don't guarantee high throughput, because warps can still stall, most often due to memory latency or synchronization. That's why you can see high GPU-Util while SM Active is low. The GPU looks busy because something is running in each sampling window, but the compute cores aren't spending most of their time doing useful math. In that case, the GPU isn't idle. It's blocked, and progress is slow.
- Understanding where bottlenecks can occur requires knowing the three paths data travels. 

1. Host to GPU (PCIe): Data moves from CPU memory to GPU memory over the PCIe bus. This is the slowest link in most systems, usually 16-32 GB/s depending on PCIe generation. 
2. GPU to GPU (NVLink): In multi-GPU systems, GPUs can transfer data directly to each other over NVLink, bypassing PCIe entirely. NVLink is much faster (300-900 GB/s depending on generation), but only matters if your workload spans multiple GPUs. 
3. GPU memory (DRAM): The GPU has its own high-bandwidth memory (HBM on datacenter GPUs, GDDR on consumer cards). When kernels run, they read inputs and write outputs to this memory

- 