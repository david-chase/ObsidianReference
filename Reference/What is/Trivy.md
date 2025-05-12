#security #artifacts #scanning #product #k8s 

https://trivy.dev/latest/

**Trivy** is a **popular open-source vulnerability scanner** designed to help developers and DevOps teams secure their container images, code, and infrastructure by detecting known security vulnerabilities, misconfigurations, and license issues.

It was developed by **Aqua Security** and is known for its simplicity, speed, and comprehensive scanning capabilities across various parts of the software supply chain.

---

## 🛠 **Key Features of Trivy**

| Feature                                          | Description                                                                                                                          |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Container Image Scanning**                     | Detects OS package vulnerabilities (e.g., in Alpine, Debian, Ubuntu) and application dependencies (Python, Node.js, Java, Go, etc.). |
| **Infrastructure as Code (IaC) Scanning**        | Scans Terraform, Kubernetes manifests, Dockerfiles, and CloudFormation for misconfigurations and security risks.                     |
| **Filesystem & Git Repository Scanning**         | Can scan a local filesystem or a Git repo to find vulnerabilities in code and dependencies.                                          |
| **SBOM (Software Bill of Materials) Generation** | Creates SBOMs in CycloneDX or SPDX formats for software supply chain transparency.                                                   |
| **License Compliance Scanning**                  | Detects problematic open-source licenses in dependencies.                                                                            |
| **Lightweight and Fast**                         | Simple CLI tool with fast local scanning and minimal setup.                                                                          |
| **Cloud Integration**                            | Can scan resources in AWS, GCP, Azure directly.                                                                                      |