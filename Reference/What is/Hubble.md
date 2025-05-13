#networking #observability #cilium

https://docs.cilium.io/en/latest/observability/hubble/index.html

**Cilium Hubble** is a **fully distributed observability platform** for **Kubernetes networking and security**, built on top of **Cilium**.

Hubble provides **deep visibility into network traffic, service-to-service communication, security policies, DNS queries, and flow logs** inside a Kubernetes cluster. It gives you **real-time observability** of how microservices are interacting — critical for debugging, performance monitoring, and security auditing.

---

## 🔹 What is Cilium?

Before Hubble:

- **Cilium** is an open-source **CNI (Container Network Interface)** plugin for Kubernetes.
    
- Built on **eBPF (Extended Berkeley Packet Filter)** technology.
    
- Provides **high-performance networking, security policies, and load balancing**.
    

---

## 🔹 What is Hubble?

- An **observability layer for Cilium-powered Kubernetes clusters**.
    
- Provides **real-time visibility into network flows and API interactions**.
    
- Offers **L3 (network), L4 (transport), and L7 (application) visibility**.
    
- Designed to monitor **east-west traffic** (internal service-to-service traffic) and **north-south traffic** (external ingress/egress).
    

---

## 🔹 Key Features of Hubble

| Feature                                    | Description                                                       |
| ------------------------------------------ | ----------------------------------------------------------------- |
| **Network Flow Visibility**                | Live view of service-to-service network flows.                    |
| **Security Policy Enforcement Monitoring** | Observe how Cilium network policies affect traffic.               |
| **DNS Visibility**                         | Tracks DNS queries and resolutions.                               |
| **HTTP & gRPC Observability**              | See L7 protocol-level request/response details.                   |
| **Flow Filtering & Aggregation**           | Query and filter flows by labels, namespaces, pods, IPs, etc.     |
| **CLI & UI Dashboards**                    | Provides `hubble observe` CLI and web UI dashboards.              |
| **Metrics Export**                         | Integration with **Prometheus** & **Grafana** for visualizations. |
| **Distributed Architecture**               | Scales with your cluster (uses eBPF for minimal overhead).        |