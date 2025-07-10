#pdb #k8s 

### ⚠️ **1. PDBs can block cluster operations**

If you set a PDB and the number of available pods falls below the `minAvailable` (or above `maxUnavailable`), Kubernetes will:

- **Prevent voluntary disruptions** like:
    - Node drains
    - Rolling updates
    - Cluster autoscaler scale-down
    - Evictions by eviction API

This can **stall operations indefinitely** if the system can't meet the PDB constraints, e.g., due to:
- Unschedulable pods (resource pressure, taints)
- Missing readiness probes (pods always count as unavailable)

---

### ⚠️ **2. PDBs only govern _voluntary_ disruptions**

They do **not** protect against:
- Node failures
- Pod crashes
- Kubelet restarts
- Preemptions (if using PriorityClasses)
- Evictions due to out-of-resource conditions (OOM, etc.)

So **you might think your app is protected — when it’s not**.

---

### ⚠️ **3. Lack of visibility**

- Kubernetes doesn’t surface **clear error messages** when PDBs block operations. You may see confusing errors when draining nodes or updating deployments.
- Observability tools don’t always highlight PDB-related bottlenecks well.

---

### ⚠️ **4. Conflicts with Cluster Autoscaler**

- The Cluster Autoscaler respects PDBs and will **refuse to evict pods** protected by one.
- If those pods are on underutilized nodes, **scale-down stalls**, leading to wasted resources and higher cost.

---

### ⚠️ **5. Readiness probe dependency**

If your pod doesn’t have a **readiness probe**, Kubernetes assumes it is **always ready**, which can cause:

- False positives in availability counts
- Improper eviction when the pod is actually unready

---

### ✅ Best Practices (to mitigate risks)

1. **Always define readiness probes** on PDB-protected pods.
2. **Use `maxUnavailable` instead of `minAvailable`** when possible — it’s easier to reason about.
3. **Test behavior under disruptions** (e.g., node drain) in staging before applying PDBs in prod.
4. **Don’t use PDBs blindly** for all apps. Stateless apps often don’t need them.
5. Use tools like **`kubectl describe pdb`** and **eviction logs** to understand disruption behavior.