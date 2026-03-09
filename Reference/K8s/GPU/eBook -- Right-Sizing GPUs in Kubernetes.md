#ebook #gpus 


## Section 2

- A GPU does not schedule threads one at a time. Instead, threads are grouped into warps, which are bundles of 32 threads that execute together. All 32 threads in a warp run the same instruction at the same time. Warps are assigned to Streaming Multiprocessors (SMs), which are the main compute units on the GPU.
- Each SM contains CUDA cores (where the math happens), shared memory, registers, and warp schedulers.
- When an SM has warps assigned and those warps are running instructions (not waiting for memory or synchronization), computation is happening. When SMs are empty or warps are stalled, the GPU is waiting.