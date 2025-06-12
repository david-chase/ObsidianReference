#virtualization #product #k8s 

https://kubevirt.io/

**KubeVirt** is an open-source project that extends Kubernetes to manage **virtual machines (VMs)** alongside containers, allowing you to **run VMs natively on Kubernetes clusters**.

---

## 🧠 What Does KubeVirt Do?

Traditionally, Kubernetes runs **containerized workloads**. KubeVirt adds the ability to run **VM-based workloads** — for example, legacy apps or OSes that can’t be easily containerized — without needing to manage a separate virtualization platform.

---

## 🧩 Key Features

|Feature|Description|
|---|---|
|🖥️ **VMs as Kubernetes objects**|VMs are defined and managed like any Kubernetes resource (YAML manifests, `kubectl`, etc.)|
|🔄 **Coexistence of VMs and containers**|You can schedule and orchestrate VMs and pods together|
|📦 **Custom Resource Definitions (CRDs)**|KubeVirt introduces CRDs like `VirtualMachine`, `VirtualMachineInstance`, `VirtualMachineInstanceReplicaSet`|
|🔐 **Secure VM isolation**|Uses **KVM** (Kernel-based Virtual Machine) for virtualization, with Kubernetes RBAC and policies|
|📈 **Live migration**|Supports VM live migration across Kubernetes nodes|
|🧰 **VM networking and storage**|Integrates with Kubernetes CNI and CSI plugins for network and persistent storage support|
|📜 **Cloud-init & guest agent**|Supports cloud-init for VM provisioning and QEMU guest agent for interaction|

---

## ⚙️ How It Works

- Built on **KVM/QEMU** under the hood (Linux virtualization)
- You install KubeVirt via an operator or Helm chart
- VMs are launched as **pods** running a QEMU process
- Management is fully declarative — VM lifecycle handled via `kubectl` or controllers

---

## 🔗 Use Cases

- 🧳 **Lift-and-shift of legacy VMs**
- 🧪 **Hybrid workloads (container + VM)**
- 🧬 **Running OSes that can't be containerized (e.g., Windows Server)**
- 🛠️ **Dev/test environments for full OS testing**
- ☁️ **Edge or private cloud virtualization on Kubernetes**