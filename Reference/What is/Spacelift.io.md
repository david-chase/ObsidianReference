#product #tool #iac #k8s

https://spacelift.io/

Spacelift is a modern Infrastructure-as-Code (IaC) automation and management platform that orchestrates Terraform, Pulumi, CloudFormation, Kubernetes manifests, and Ansible in a secure, policy-driven workflow. It acts as a CI/CD layer purpose-built for IaC, providing GitOps-style automation, drift detection, security guardrails, and strong policy controls using Open Policy Agent (OPA).

It’s often described as **“Terraform Cloud on steroids”** — but for multiple IaC frameworks.

---
## 🧩 **Key Features**

- **Multi-IaC Support**  
    Terraform, Terragrunt, Pulumi, CloudFormation, Ansible, Kubernetes YAML.
- **GitOps-Driven Automation**  
    Automatically plans and applies on PRs and merges.
- **Powerful Policy Engine**  
    Uses **OPA/Rego** for fine-grained security, compliance, workflow rules, approval logic, etc.
- **Drift Detection**  
    Monitors your cloud infra for changes made outside IaC and alerts you.
- **Stacks & Workflows**  
    Define reusable, composable IaC environments and pipelines.
- **Secrets Management**  
    Built-in secure storage or integration with cloud KMS/secret managers.
- **RBAC and SSO**  
    Enterprise-grade access controls.
- **Private Workers / Runners**  
    Run Spacelift jobs in your own VPC for fully isolated builds.

---

## 🏗️ **Common Use Cases**

- Enforcing compliance and governance in Terraform workflows
- Managing large Terraform/Terragrunt monorepos
- Multi-cloud IaC deployment pipelines
- Detecting and remediating infrastructure drift
- Isolated IaC execution for regulated environments
- Unifying Terraform, Kubernetes, and Pulumi deployments under a single control plane

---

## 📦 **Architecture Highlights**

- Git → triggers **Stacks** → triggers **Runners/Workers**
- Policies (OPA) enforce rules around planning, applying, approvals
- State stored in Spacelift or external backends (S3, GCS, Azure Blob, etc.)
- Integrates with GitHub, GitLab, Bitbucket, Azure DevOps, Okta, Vault, cloud providers, Kubernetes

---

## 🔍 **Why Teams Use Spacelift**

- Strongest governance story in the IaC ecosystem
- Exceptionally good for **large-scale Terraform or Terragrunt**
- Better CI/CD for IaC than general-purpose CI tools
- Cleaner, more flexible policies than Terraform Cloud
- Enterprise-ready with private workers and deep integrations