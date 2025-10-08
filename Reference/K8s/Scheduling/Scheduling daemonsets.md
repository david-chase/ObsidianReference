#scheduling #k8s #daemonsets #nodes 

When a new node joins a cluster (or node group):

1. **Kubelet registers the node** with the API server.
2. The **DaemonSet controller** watches for new nodes and immediately creates the necessary DaemonSet pods targeting that node (based on node selectors, taints, tolerations, etc.).
3. These DaemonSet pods **bypass the default scheduler** — they’re placed directly by the DaemonSet controller onto the node.
4. Then, **normal workload pods (Deployments, StatefulSets, Jobs, etc.)** are scheduled by the Kubernetes scheduler as usual.

---

### 🔹 Why it _seems_ like DaemonSets go first

- Since the DaemonSet controller acts immediately upon node registration, its pods appear on the node almost instantly.
- The regular scheduler works asynchronously and only sees a “ready” node after kubelet updates it, so normal pods are slightly delayed in comparison.
- Therefore, DaemonSet pods _appear first_ — but this is not due to scheduler priority; it’s due to the **DaemonSet controller’s direct placement mechanism**.

---

### 🔹 Implications

- **Resource competition**: DaemonSet pods can reserve CPU/memory before normal workloads are scheduled, which is often intentional (e.g., node-exporter, CNI pods, logging agents).
- **Best practice**: Always set **resource requests and limits** on DaemonSet containers to ensure node capacity is correctly accounted for before other pods are scheduled.

---

So, in summary:

> ✅ DaemonSets _do not have a scheduler priority_, but they **are created first** on new nodes because their controller directly places them there as soon as the node appears.