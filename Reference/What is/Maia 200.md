#chip #microsoft #inference #ai 

[https://news.microsoft.com/maia-200/](https://news.microsoft.com/maia-200/)

The Maia 200 is Microsoft’s second-generation custom AI accelerator, specifically engineered to optimize large-scale AI inference workloads within the Azure cloud infrastructure. Built on a cutting-edge 3-nanometer process, it is designed to power modern reasoning models, large language models (LLMs), and agentic AI workflows. The architecture focuses on improving the economics of AI by maximizing performance-per-dollar and energy efficiency, particularly for high-throughput token generation tasks such as those required by OpenAI's GPT-5.2 and Microsoft 365 Copilot.

## Technical Architecture

- Process Technology: Fabricated using TSMC 3nm (N3) process node.
- Transistor Count: Contains over 140 billion transistors.
- Compute Units: Features Tile Tensor Units (TTU) for matrix operations and Tile Vector Processors (TVP) for programmable SIMD tasks.
- Precision Support: Optimized for narrow-precision formats including FP4, FP6, and FP8, as well as mixed-precision modes.
- Thermal Design: Operates within a 750W System-on-Chip (SoC) Thermal Design Power (TDP) envelope.

## Memory Subsystem

- High Bandwidth Memory: Equipped with 216GB of HBM3e.
- Memory Bandwidth: Delivers 7 TB/s of HBM bandwidth.
- On-Die SRAM: Features 272MB of software-managed on-chip SRAM to minimize off-chip data movement.
- Data Movement: Utilizes specialized DMA engines and a hierarchical Network-on-Chip (NoC) to reduce bottlenecks.

## Networking and Scalability

- Interconnect: Includes an integrated NIC and an Ethernet-based scale-up fabric.
- Scale-up Bandwidth: Provides 2.8 TB/s of dedicated bidirectional bandwidth per accelerator.
- Cluster Capacity: Supports scaling to clusters of up to 6,144 accelerators.
- Topology: Uses a "Fully Connected Quad" (FCQ) design within trays to allow four accelerators to communicate via direct, non-switched links.

## Software and Ecosystem

- Maia SDK: A comprehensive software development kit that includes a Triton compiler, PyTorch integration, and optimized kernel libraries.
- Programming: Supports a low-level Nested Parallel Language (NPL) for fine-grained control over data placement and movement.
- Deployment: Integrated into Azure AI Foundry and utilized by Microsoft's Superintelligence team for synthetic data generation and reinforcement learning.