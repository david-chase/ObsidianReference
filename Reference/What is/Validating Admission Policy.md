#policy #k8s 

**Validating Admission Policy (VAP)** is a Kubernetes feature introduced as **stable in v1.30**, which provides a declarative, native way to validate Kubernetes API requests **without writing external admission webhooks**. It enables cluster administrators to enforce policies on objects being created or updated, similar in spirit to tools like Open Policy Agent (OPA) or Kyverno, but built directly into Kubernetes using a **CRD-based model**.

---

### 🔍 **Key Features**

- ✅ **Built-in alternative to validating admission webhooks**
- 📜 **Declarative**: Uses CRDs like `ValidatingAdmissionPolicy` and `ValidatingAdmissionPolicyBinding`
- 🧠 **CEL-based validation logic** (Common Expression Language)
- 🔄 **Dynamic updates**: Can update rules without restarting components
- 🔐 **Does not require extra services** (e.g., no external webhook servers)

---

### 🧱 **Core Components**

- **`ValidatingAdmissionPolicy`**
    - Defines the validation logic using CEL expressions
    - Specifies match criteria (e.g., `apiGroups`, `resources`)
    - Optionally includes parameter CRDs for external input
- **`ValidatingAdmissionPolicyBinding`**
    - Binds policies to specific users, groups, or service accounts
    - Enables flexible RBAC-style policy enforcement

---

### 🔧 **Example Use Cases**

- Require all namespaces to have specific labels
- Ensure `runAsNonRoot` is set in all pod security contexts
- Block creation of services with `type=NodePort`
- Enforce naming conventions for resources

---

### 📌 **Advantages over Webhooks**

- No network latency or TLS management
- Native to Kubernetes, easier to maintain and scale
- CEL expressions provide powerful and efficient logic
- Works across API servers without extra components