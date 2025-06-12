#cni #k8s #networking #product 

https://github.com/kubeovn/kube-ovn

**Kube-OVN** is a **Kubernetes network plugin** based on [Open vSwitch (OVS)](https://www.openvswitch.org/) and [OVN (Open Virtual Network)](https://github.com/ovn-org/ovn), designed to provide **flexible, scalable, and high-performance networking** for Kubernetes clusters. It combines native Kubernetes experience with powerful features from the SDN (Software Defined Networking) world, such as VLAN, subnet isolation, QoS, IP management, and more.

---

### 🔑 **Key Features**

- **Integrated CNI Plugin:** Seamless integration with Kubernetes via Container Network Interface (CNI).
- **Overlay and Underlay Support:** Supports both Geneve/VXLAN overlays and VLAN-based underlays.
- **Built-in Subnet Management:**
    - Custom subnet configuration
    - IP and MAC address management
- **Policy and Security:**
    - Network Policies compatible with Kubernetes
    - ACLs and Security Groups for fine-grained control
- **QoS and Traffic Control:** Per-pod ingress/egress rate limiting
- **Gateway Support:**
    - Centralized or distributed gateway modes
    - North-South traffic handling
- **BGP and Egress Gateway:** Native BGP support and SNAT capabilities
- **Load Balancing:** Integrated support via OVN’s native LB mechanism
- **Dual Stack Support:** IPv4 and IPv6

---

### 📦 **Components**

- `kube-ovn-controller`: Syncs K8s resources with OVN and manages IP/MAC assignments.
- `kube-ovn-cni`: The CNI plugin invoked during pod network setup.
- `kube-ovn-monitor`: Provides visibility into network performance and health.
- `kube-ovn-pinger`: Health checker for overlay network.
- `kube-ovn-admission`: Validating webhook for CRDs and configuration.

---

### ⚙️ **Advanced Capabilities**

- Multiple subnets per namespace
- Static IP/MAC assignments
- Namespace and pod network isolation
- Native support for stateful workloads
- Support for hybrid cloud/multi-cluster networking

---

### 🚀 **Use Cases**

- Enterprises needing fine-grained, isolated networking in Kubernetes
- Kubernetes clusters requiring overlay/underlay hybrid networks
- Multi-tenant environments with per-tenant subnet control
- Bare-metal Kubernetes clusters with VLAN support