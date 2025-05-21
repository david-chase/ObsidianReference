#dns #networking #product #k8s 

https://coredns.io/

**CoreDNS** is a **DNS server** that is commonly used as the **default DNS provider in Kubernetes clusters**. It provides **name resolution for services, pods, and external domains** inside the cluster.

---

## 🔹 What is CoreDNS?

- An **open-source, flexible, and extensible DNS server**.
    
- Written in **Go**.
    
- Designed to be a **cloud-native DNS solution**.
    
- It replaced **kube-dns** as the default DNS server in Kubernetes since version **1.13**.
    

---

## 🔹 CoreDNS in Kubernetes

In a Kubernetes cluster:

- When a **pod needs to resolve a service name** (e.g., `my-service.my-namespace.svc.cluster.local`), the request is handled by **CoreDNS**.
    
- CoreDNS runs as a **Deployment in the kube-system namespace**.
    
- It listens on a **ClusterIP service (typically 10.96.0.10)**.
    
- Kubernetes nodes configure pods to use this IP as their DNS resolver.