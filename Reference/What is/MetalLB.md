#k8s #networking #loadbalancing #product

https://metallb.io/

**MetalLB** is an **open-source load-balancer implementation for bare-metal Kubernetes clusters**. It provides **LoadBalancer-type services** functionality, which is typically only available in cloud environments (like AWS ELB, GCP LB, Azure LB), but for **on-premises and bare-metal setups**.

---

## 🔹 Why MetalLB?

- In **cloud Kubernetes**, `ServiceType: LoadBalancer` automatically provisions a cloud load balancer.
    
- On **bare-metal clusters**, there’s no native load balancer API.
    
- MetalLB fills this gap by providing a **software load balancer** that integrates with Kubernetes.
    

---

## 🔹 Key Features

| Feature                              | Description                                                |
| ------------------------------------ | ---------------------------------------------------------- |
| **Implements LoadBalancer Services** | Allows `type: LoadBalancer` to function on bare-metal      |
| **Supports Layer 2 (ARP/NDP)**       | Uses ARP/NDP to advertise service IPs on the local network |
| **Supports BGP (Layer 3)**           | Can peer with routers via BGP to announce service IPs      |
| **Simple to Deploy**                 | Runs as standard Kubernetes pods                           |
| **IP Address Pool Management**       | Manages pools of IPs for services                          |
| **No Vendor Lock-in**                | Pure open-source solution                                  |