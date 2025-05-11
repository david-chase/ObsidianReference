#k8s #product #networking #cni 

https://www.tigera.io/project-calico/

**Calico** is an open-source networking and network security solution for containers, virtual machines, and Kubernetes clusters. It provides scalable, high-performance networking and security features for cloud-native applications.

Calico is widely used as a **CNI (Container Network Interface) plugin** in Kubernetes but also supports non-Kubernetes environments.

---

## 🛠 **What Does Calico Do?**

|Feature|Description|
|---|---|
|**Container Networking (CNI)**|Provides Layer 3 networking for Kubernetes pods using standard routing protocols.|
|**Network Security**|Implements Kubernetes NetworkPolicies for controlling pod-to-pod traffic and extends it with global network policies.|
|**IP Address Management (IPAM)**|Manages IP address assignment for pods efficiently.|
|**Egress Policies**|Controls outbound traffic from pods to external networks.|
|**Service Graphs and Visibility**|Integrates with observability tools for monitoring network traffic.|
|**Hybrid Cloud Networking**|Works across on-premises, hybrid, and multi-cloud environments.|

---

## 🔧 **Key Features**

- **Pure Layer 3 Networking**: Uses BGP (Border Gateway Protocol) for scalable, high-performance pod communication.
    
- **Network Policies**: Supports Kubernetes-native network policies and advanced Calico-specific policies.
    
- **Encryption**: Provides IP-in-IP and WireGuard encryption for pod-to-pod traffic.
    
- **eBPF Mode**: Calico supports eBPF data plane mode (like Cilium) for enhanced performance.
    
- **Flexible IPAM Modes**: Supports various IP address management strategies (Calico IPAM, Kubernetes IPAM, etc.).
    
- **Calico Enterprise**: A commercial version with advanced features like application layer policies, flow logs, and compliance reporting.

