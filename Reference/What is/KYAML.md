#yaml #markup #k8s #definition 

**KYAML** (pronounced “K-yaml”) is **a Go library and toolkit created by the Kubernetes SIG CLI** (the same group behind `kustomize`) for **reading, writing, transforming, and validating YAML files that represent Kubernetes resources**.

In other words:

> KYAML is a **Kubernetes-aware YAML processor** — it understands Kubernetes object structure, metadata, and conventions.

It’s not a new data format. It’s still YAML under the hood — but KYAML adds intelligence and structure to how YAML manifests are handled.

---

### 🧾 What **YAML** Is

**YAML (YAML Ain’t Markup Language)** is just a **data serialization language** — a way to represent structured data using indentation.  
Kubernetes uses YAML to define manifests (Pods, Deployments, Services, etc.), but YAML itself is generic — it has no idea what a “Pod” or a “metadata.name” is.

---

### ⚖️ Key Differences Between KYAML and YAML

| Aspect                | YAML                                                  | KYAML                                                                        |
| --------------------- | ----------------------------------------------------- | ---------------------------------------------------------------------------- |
| **Definition**        | Generic human-readable data format                    | Kubernetes-specific YAML processing library                                  |
| **Purpose**           | Serialize data in a readable text form                | Parse, modify, and write Kubernetes resource YAMLs safely and predictably    |
| **Context awareness** | Has no schema or semantic understanding               | Knows about Kubernetes resource structure (apiVersion, kind, metadata, etc.) |
| **Validation**        | None built-in                                         | Can perform schema validation and structural transformations                 |
| **Language**          | Format specification only                             | Go library + CLI utilities (used by `kustomize`, `kubectl kustomize`, etc.)  |
| **Mutation support**  | You edit manually                                     | Provides APIs to programmatically mutate or traverse nodes                   |
| **Tooling**           | Used everywhere (Ansible, Helm, GitHub Actions, etc.) | Mostly used inside Kubernetes-related Go tools and plugins                   |

