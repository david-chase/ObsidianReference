#cicd #tool #k8s #deployment 

https://kargo.akuity.io

**Kargo** is an open-source continuous delivery (CD) tool for Kubernetes, created by [Akuity](https://akuity.io) (the company behind Argo CD). It manages progressive promotion of container images and Helm/Kustomize deployments **across environments**, such as dev → staging → prod, based on approval gates or automated checks.

Kargo excels at **environment promotion workflows**, solving a common gap in GitOps by introducing multi-environment awareness and safe, automated progression between them.

---

### 🔑 Key Features

- **Multi-environment promotions**
    - Promotes workloads across environments automatically or with manual approval.
    - Integrates seamlessly with GitOps workflows.
- **Support for popular deployment tools**
    - Works with **Helm**, **Kustomize**, and **raw Kubernetes manifests**.
    - Supports OCI-based Helm charts and Kustomize artifacts.
- **Flexible promotion gates**
    - Supports **manual approvals**, **metric checks**, **test results**, or **custom conditions** before promoting.
- **Promotion plans**
    - Declaratively define how and when images should be promoted across environments.
- **Native GitOps integration**
    - Uses Git as the source of truth for promotion decisions.
    - Compatible with Argo CD, Flux, and other GitOps tools.

---

### 🛠️ Technical Highlights

- Written in **Go**
- Runs as a **Kubernetes controller**
- Uses **CRDs** to define environments and promotion workflows
- **RBAC**-aware and namespace-scoped by design

---

### 📦 Typical Use Case

A user deploys an image to `dev` and wants it to move to `staging` only if:

- Tests pass
- SLOs are met
- A manual approval is given

Kargo automates and tracks this promotion, avoiding manual re-tagging or copy/paste of values between environment overlays.

---

### 🔧 Components

- **Freight**: A deployable version of a workload (e.g., container image + Helm values)
- **Warehouse**: A staging area that holds valid Freights
- **Environment**: A GitOps-managed target environment
- **PromotionPolicy**: Defines how promotions happen between environments