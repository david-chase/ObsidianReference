#product #finops #iac

https://www.infracost.io/

💡 **Overview**  
**Infracost** is an open-source tool that helps developers and DevOps teams estimate the **cost of cloud infrastructure changes** **before** they are deployed. It integrates directly into infrastructure-as-code (IaC) workflows such as **Terraform**, **Terragrunt**, and **Pulumi**, showing the financial impact of each proposed change in pull requests or CI/CD pipelines.

It’s designed to bring **FinOps visibility** into development workflows, helping teams control cloud spend proactively rather than reactively.

---

⚙️ **Key Features**

- **💰 Cost Estimates in CI/CD:**  
    Automatically shows cost differences (`+/-`) for Terraform or Pulumi changes in pull requests.
- **🌩️ Multi-Cloud Support:**  
    Works with major providers — **AWS**, **Azure**, **Google Cloud**, and **Alibaba Cloud**.
- **📦 IaC Integrations:**  
    Native support for **Terraform**, **Terragrunt**, **Pulumi**, and **Atlantis**.
- **🔍 Detailed Cost Breakdown:**  
    Provides per-resource and per-service breakdowns (e.g., EC2, S3, EKS).
- **🧩 API & Dashboard:**  
    The **Infracost Cloud** service adds a central dashboard, usage-based pricing, budgets, and team management.
- **🔒 Open-Source CLI:**  
    The CLI runs locally and doesn’t send Terraform code to remote servers by default.

---

🧠 **Typical Workflow**

1. Developer modifies Terraform code.
2. Infracost runs during CI/CD or via CLI.
3. It parses the Terraform plan and calculates cost changes.
4. It posts a PR comment showing the **monthly cost impact** of the change.

Example PR comment:

> 💵 **Monthly cost change:** +$57.40 (+3%)
> - Added: 1 × `aws_instance.t3.medium` → $33.41/mo
> - Added: 1 × `aws_ebs_volume` → $24.00/mo

---

📊 **Common Use Cases**

- **FinOps integration** with engineering workflows.
- **Budget enforcement** during CI/CD.
- **Cost impact visibility** for Kubernetes clusters or autoscaling resources.
- **Cloud migration planning** and scenario analysis.

---

🏗️ **Tech & Deployment Options**

- CLI: install via `brew install infracost` or Docker.
- Integrates with GitHub Actions, GitLab CI, Jenkins, CircleCI, Azure DevOps, and Bitbucket.
- Can export reports as JSON, HTML, or Markdown for automation pipelines.