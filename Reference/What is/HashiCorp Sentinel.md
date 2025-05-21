#framework #hashicorp #ibm #pac #policy

https://www.hashicorp.com/en/sentinel

**HashiCorp Sentinel** is a **policy as code framework** that enables you to define and enforce **fine-grained, logic-based policies** across HashiCorp products and workflows. It allows organizations to implement **compliance, governance, and security policies** in a **programmatic and automated** way.

Sentinel is deeply integrated into HashiCorp tools like **Terraform Enterprise/Cloud, Vault, Nomad, and Consul**, providing policy enforcement at various stages of the infrastructure lifecycle.

---

## 🔹 What is Sentinel?

- A **policy as code** framework by **HashiCorp**.
    
- Used to **codify governance policies** using a high-level, declarative language.
    
- Ensures **infrastructure changes, secrets management, and service networking** comply with organizational standards.
    
- Allows **automated policy enforcement** before actions are taken (e.g., before applying a Terraform plan).
    

---

## 🔹 Key Features of Sentinel

| Feature                            | Description                                                                 |
| ---------------------------------- | --------------------------------------------------------------------------- |
| **Policy as Code (PaC)**           | Write policies in a purpose-built language to enforce organizational rules. |
| **Fine-Grained Enforcement**       | Policies can control specific resource attributes and workflows.            |
| **Embedded in HashiCorp Products** | Integrated with Terraform, Vault, Nomad, Consul.                            |
| **Multiple Enforcement Levels**    | Advisory, Soft-Mandatory, Hard-Mandatory enforcement modes.                 |
| **Extensible Imports**             | Use external data sources (e.g., Terraform plans, Vault data) in policies.  |
| **Simulation Mode**                | Test and validate policies before enforcing.                                |
| **Governance & Compliance**        | Ensures infrastructure adheres to security, cost, and compliance policies.  |