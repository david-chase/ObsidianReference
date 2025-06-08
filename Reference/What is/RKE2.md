#rancher #suse #k8s #distribution 

https://docs.rke2.io/

**RKE2** stands for **Rancher Kubernetes Engine 2**. It's a **secure, compliant, and production-ready Kubernetes distribution** developed by SUSE (formerly Rancher Labs). It is the next-generation replacement for the original RKE, designed with modern best practices and security in mind.

---

### 🔍 **What RKE2 Is**

- A **CNCF-conformant Kubernetes distribution**
- Built to meet **CIS Kubernetes Benchmark** out of the box
- Uses **containerd** as the container runtime (not Docker)
- Focused on **stability, security, and compliance** for production workloads
- Fully open source

---

### ⚙️ **Key Features**

|Feature|Description|
|---|---|
|**Security-First Design**|Includes SELinux support, audit logging, and FIPS 140-2 compliance options.|
|**Container Runtime**|Uses **containerd** (Docker is deprecated).|
|**ETCD Embedded**|Uses an embedded etcd for simplicity unless external etcd is configured.|
|**Air-Gap Friendly**|Can run without internet access; all images can be preloaded.|
|**Declarative Install**|Uses YAML-based config files for installation, allowing GitOps compatibility.|
|**Systemd Integration**|Installs as a systemd service, making it easier to manage.|
|**SELinux & AppArmor**|Enforces kernel security modules where supported.|
|**CIS Hardened Profiles**|Default settings meet the Kubernetes CIS security benchmarks.|

---

### 🔧 **Use Cases**

- High-security environments (e.g., finance, government, healthcare)
    
- On-prem, cloud, edge, or air-gapped infrastructure
    
- As the runtime for clusters managed by **Rancher**
    
- As a standalone, self-managed Kubernetes cluster