#etcd #k8s #encryption #csp #comparison #chatgpt
# etcd Encryption Defaults for Managed Kubernetes Services

This document summarizes the default behavior of etcd encryption at rest for major managed Kubernetes service providers.

---

## ✅ EKS (Amazon Elastic Kubernetes Service)

- **etcd encryption at rest is _not enabled by default_.**
- You **must opt in** during cluster creation by enabling **encryption with a KMS key**.
- Once set, it **cannot be changed**.

📌 [AWS Docs - EKS Secrets Encryption](https://docs.aws.amazon.com/eks/latest/userguide/encrypt-secrets.html)

---

## ✅ AKS (Azure Kubernetes Service)

- **etcd encryption at rest is enabled by default.**
- Azure uses **Azure-managed keys**.
- Optionally, you can use **customer-managed keys (CMKs)**.

📌 [Microsoft Docs - Data encryption in AKS](https://learn.microsoft.com/en-us/azure/aks/security-baseline#5-data-protection)

---

## ❌ GKE (Google Kubernetes Engine)

- **etcd encryption is _not enabled by default_** for most standard clusters.
- You can **enable Customer-Managed Encryption Keys (CMEK)** to encrypt etcd.
- Autopilot clusters also don’t have etcd encryption enabled by default.

📌 [Google Docs - GKE CMEK](https://cloud.google.com/kubernetes-engine/docs/how-to/customer-managed-encryption)

---

## ❌ OKE (Oracle Kubernetes Engine)

- **etcd encryption is _not enabled by default_**.
- Oracle Cloud does encrypt other storage services by default, but for etcd encryption, you need to configure it manually.

📌 [Oracle Docs - OKE Security](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengsettingupcsi.htm)

---

## Summary Table

| Provider | etcd Encryption at Rest | Default | Notes |
|----------|-------------------------|---------|-------|
| **EKS**  | Yes                     | ❌       | Must enable during cluster creation |
| **AKS**  | Yes                     | ✅       | Enabled by default with option for CMKs |
| **GKE**  | Yes                     | ❌       | Optional with CMEK |
| **OKE**  | Manual setup            | ❌       | Not enabled by default |
