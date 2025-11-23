#k8s #autoscaling 

## Common Reasons for Node Downscale Block (Both Cluster Autoscaler & Karpenter)**

### 1. Non-evictable pods (e.g., DaemonSets or pods with `local storage`)
- **DaemonSets**: Run on every node and are not evicted during scale-down.
- **Pods with local storage** (e.g., `emptyDir`, `hostPath`): Eviction may cause data loss, so autoscalers avoid disrupting them.
- **Pods with `podDisruptionBudget` violations**: If a scale-down would violate a PDB, it's blocked.  This will always block if a PDB is set to 0.
- **Pods not managed by a controller** (like bare `kubectl run` pods): They’re not safe to reschedule, so the node stays.
- `safe-to-evict` label is set to `false`

### 2. Pods with node affinity or anti-affinity rules
- If a pod can **only run** on specific node types (e.g., `requiredDuringSchedulingIgnoredDuringExecution`), the autoscaler won’t remove a node if it’s the only place such pods can live.

### 3. Resource fragmentation
- If pods **can’t be rescheduled elsewhere** due to resource constraints (CPU/mem), even if a node looks underutilized, it may stay online.
- Example: Node has 500m CPU left, but the only pod to schedule needs 600m.

### 4. Pods in `Terminating` or `Unknown` state
- Karpenter and Cluster Autoscaler will wait until terminating pods finish.
- Nodes hosting `Unknown` pods may be considered tainted and not downsized.


---

## Cluster Autoscaler-Specific Limitations

### 5. Minimum node group size
- If the node group’s `minSize` is >0, CA won't go below it—even if the node is empty.

### 6. Node utilization thresholds not met
- CA uses a **utilization threshold** (default 50% for CPU/mem). A node won’t be removed if it’s considered “in use.”

### 7. Custom annotations preventing scale-down
- `cluster-autoscaler.kubernetes.io/scale-down-disabled` on nodes or pods prevents downsizing.

---

## Karpenter-Specific Scenarios

### 8. TTL Settings Not Met
- Karpenter uses `TTLSecondsAfterEmpty` and `TTLSecondsUntilExpired`. If these haven’t elapsed, the node stays.

### 9. Drift detection policies
- If a node has drift (e.g., wrong AMI, taints, labels), Karpenter may keep or replace it instead of downsizing based on usage.

### 10. Linked volumes or constraints
- If a pod is using a volume that’s not reattachable or reschedulable (e.g., a `hostPath` volume), Karpenter can’t drain the pod safely.