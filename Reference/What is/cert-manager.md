#k8s #certificates #security #product 

https://cert-manager.io/

**cert-manager** is an **open-source Kubernetes add-on** that **automates the management of TLS/SSL certificates** within Kubernetes clusters.

It helps you:

- Automatically **provision, renew, and manage TLS certificates**.
    
- Integrates with **public CAs** (like Let's Encrypt) and **internal PKIs**.
    
- Manages certificates for **Ingress resources**, **Services**, **webhooks**, and **mutual TLS**.
    

---

## 🔹 What cert-manager Does:

| Feature                            | Description                                                          |
| ---------------------------------- | -------------------------------------------------------------------- |
| **Automated Certificate Issuance** | Requests and provisions TLS certificates for services & ingress      |
| **Automated Renewal**              | Automatically renews expiring certificates                           |
| **Supports Multiple Issuers**      | Works with Let's Encrypt, Venafi, HashiCorp Vault, self-signed, etc. |
| **Kubernetes-native CRDs**         | Uses Kubernetes resources to manage certificates declaratively       |
| **Integrates with Ingress**        | Automatically secures ingress endpoints with HTTPS                   |
| **Supports ACME Protocol**         | For public CAs like Let's Encrypt                                    |