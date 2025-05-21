#k8s #product #virtualization 

https://www.vcluster.com/

**vCluster** (short for **Virtual Cluster**) is an **open-source project** developed by **Loft Labs** that allows you to create **virtual Kubernetes clusters** on top of existing Kubernetes clusters.

It is a lightweight solution to **run multiple isolated Kubernetes control planes** inside a single physical Kubernetes cluster. This is especially useful for **multi-tenancy, testing, CI/CD, and developer environments**.

---

## 🔹 What is vCluster?

- vCluster creates **"virtual" Kubernetes clusters** by deploying a **Kubernetes control plane (API server & components) as pods** inside a namespace of a **host cluster**.
    
- To users and tools, a vCluster looks and behaves like a regular Kubernetes cluster.
    
- It enables **namespace-level multi-tenancy with strong isolation**, but at a fraction of the resource cost compared to spinning up full clusters.
    

---

## 🔹 Key Features

|Feature|Description|
|---|---|
|**Virtual Kubernetes Clusters**|Fully functional K8s clusters inside a namespace of a host cluster.|
|**Control Plane Isolation**|Each vCluster has its own API server, etcd (or etcd-like backend).|
|**Resource Efficiency**|No need to provision full K8s clusters per tenant.|
|**RBAC & Network Isolation**|Supports isolation between vClusters and between tenants.|
|**Supports Helm & kubectl**|vClusters behave like real clusters (works with kubectl, Helm, Argo CD, etc.).|
|**Ephemeral Dev/Test Environments**|Quickly spin up disposable clusters for testing & CI/CD.|