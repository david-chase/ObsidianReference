#ai #grove #howto #k8s #nvidia #orchestration #tutorial

Kubernetes changed how we run applications, but not how we run **AI**.

It was designed for **microservices**: small, independent apps that can scale up and down without much concern for each other.

**AI models are different**.

They are large, connected systems where several components must work together. One model might require multiple pods to handle different tasks simultaneously: some preparing the input, others generating the output, all exchanging data constantly.

Try running that with vanilla Kubernetes, and things start to break fast.

That’s why NVIDIA created **Grove,** a new way to **orchestrate** **complex AI systems** directly on **Kubernetes**.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1050/1*wlnGxzMHbUVzI-r2nW8spQ.png)

## The Orchestration Gap Kubernetes Can’t Solve Alone

Kubernetes excels at running **stateless** workloads, such as web APIs or backend services.  
But modern AI inference, which is the process of using a trained model to generate predictions or text, is anything but stateless.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1050/1*5QMmLVDA2EGWA0HumthPmg.png)

Here’s why:

- **Large models span multiple GPUs**  
    Large AI models are often _sharded_, meaning they’re split across several nodes. A single model instance might involve five or ten pods working together. Scaling only one pod doesn’t work when the real scaling unit is the entire group.
- **Startup ordering**  
    Certain components must start in a specific sequence. For example, worker pods need to be ready before the leader pod initializes. **Kubernetes doesn’t enforce this logic natively**.
- **Gang scheduling**  
    Some pods must start together to be useful. Imagine launching a decode pod without a prefill pod, the system can’t function. Without “all-or-nothing” scheduling, GPUs sit idle waiting for missing components.
- **Topology awareness**  
    AI components exchange huge amounts of data. If those pods end up on nodes without high-speed connections (like [NVLink](https://www.nvidia.com/en-us/products/workstations/nvlink-bridges/)), performance collapses. Placement matters, a lot.

In short: Kubernetes knows how to scale _pods_.  
AI needs a way to scale **_systems_**_:_ groups of pods that work as one.

## Meet NVIDIA Grove

Grove is a **Kubernetes API** that finally makes the platform aware of AI.

It gives you **one unified interface** to describe and run any inference workload — from a lightweight, single-pod model to a multi-node architecture spanning thousands of GPUs.

Instead of juggling multiple YAML files or custom controllers, you define your entire serving system, prefill, decode, routing, and every other component, in **one declarative spec**.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1050/1*bC439zZbKIu-Fd6ifZDCBQ.png)

NVIDIA Grove

From that single definition, Grove handles all the orchestration behind the scenes: deciding **when** each component starts, **where** it runs, and **how** the system scales as demand grows.

That’s possible because Grove understands the relationships inside your AI workload. It can:

- **Coordinate pods** that must launch together so the system never half-starts.
- Place components close to each other on the network to **minimize** **latency**.
- Scale different roles independently to match **real-time demand**.
- **Control startup order** so everything initializes in the right sequence.

In short, **Grove** treats your AI model as one unified system, not a collection of pods.

## How Grove Works: Four Core Concepts

Grove introduces a few key primitives that describe how your model behaves and how its parts work together inside Kubernetes.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1050/1*t_TEHBUSS16V-WXbE7QxEQ.png)

**🔹️ PodCliqueSet:** At the top of the hierarchy, the PodCliqueSet defines the complete inference system. It governs how components scale, start, and are placed across nodes, ensuring the system behaves as one coordinated workload.

**🔹 PodClique:** A group of pods that share the same role, such as a set of decode workers or a frontend router. This allows each role in your architecture to be configured, managed, and scaled independently.

**🔹 PodCliqueScalingGroup:** A collection of PodCliques that need to run and scale together. It keeps interdependent components, like prefill and decode, synchronized so the entire system starts and operates cohesively.

**🔹️ PodGang:** The **link** between Grove and the Kubernetes scheduler. It ensures that groups of related pods are deployed together as a single unit, waiting if necessary until all required resources are available.

## Quick Start: Try Grove in 5 Minutes

If your Kubernetes cluster is already set up (and `kubectl` is configured), you can get Grove running in just a few commands.

### 1. Install **Grove**

git clone https://github.com/NVIDIA/grove.git  
cd grove  
make deploy  
  
kubectl get pods -n grove-system

This installs the Grove **operator**, **CRDs**, and supporting components.

### 2. Deploy a Sample Workload

kubectl apply -f samples/simple/simple1.yaml

This launches a **minimal example** showing Grove’s orchestration in action.

### 3. Inspect Grove Resources

kubectl get pcs,pclq,pcsg,pg,pod -o wide

You’ll see Grove-created objects like `PodClique`, `PodCliqueSet`, and `PodGang`, which coordinate your workload as a single logical unit.

For cloud and production deployments, NVIDIA provides full installation and configuration guides in the [official docs](https://github.com/NVIDIA).

## Example Use Cases

Grove simplifies AI workload orchestration across any setup:

- **Multi-GPU inference:** Run large models like DeepSeek-R1 or Llama-4 with coordinated scaling and placement.
- **Real-life chatbot:** A multilingual bot (router + prefill + decode pods) defined in one YAML. Grove spawns all components together, keeps them synced, and scales each role independently.
- **Agentic pipelines:** Orchestrate multiple collaborating models with correct startup and topology awareness.
- **Single-GPU tasks:** Even simple models get consistent, automated orchestration.

In short, Grove turns complex, multi-component AI deployments into a single, declarative workflow.