#k8s #product #networking #cni 

https://github.com/flannel-io/flannel

**Flannel** is a simple and lightweight **CNI (Container Network Interface)** plugin for Kubernetes that provides **pod networking** by creating an **overlay network**. It's one of the earliest and most widely adopted solutions for enabling network communication between pods across different nodes in a Kubernetes cluster.

---

## 🛠 **What Does Flannel Do?**

- Assigns **each node** in your Kubernetes cluster a **unique subnet**.
    
- Ensures that **every pod** gets a unique IP address.
    
- Uses various **backends** (VXLAN, host-gw, AWS VPC, etc.) to handle cross-node pod communication.
    
- Allows pods on different nodes to communicate as if they were on the same local network.