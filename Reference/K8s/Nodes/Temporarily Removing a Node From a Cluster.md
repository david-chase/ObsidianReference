#k8s #nodes 

This guide describes the **easiest, fully reversible way** to simulate a node being removed (scaled in) from a Kubernetes cluster **without** using an autoscaler.

This method ensures the node **disappears from `kubectl get nodes`**, triggers normal rescheduling behavior, and can be cleanly re-added later.

---

## Prerequisites

- Cluster installed with `kubeadm`
- SSH access to the node being removed
- Cluster-admin access from a control-plane node or workstation
- Node name known (example: `miami`)

---

## Overview of the Approach

1. Stop the kubelet on the target node  
2. Delete the Node object from the Kubernetes API  
3. Observe pod rescheduling and node disappearance  
4. Restart kubelet to automatically rejoin the node  

This works because:
- Nodes self-register with the API when kubelet is running
- A stopped kubelet cannot recreate its Node object
- Restarting kubelet causes the node to reappear automatically

---

## Step 1 — Stop kubelet on the target node

SSH into the node you want to temporarily remove:

```powershell
sudo systemctl stop kubelet
```

Result:
- Node transitions to `NotReady`
- Node still appears in `kubectl get nodes` (for now)

---

## Step 2 — Delete the Node object from the cluster

From a control-plane node or workstation:

```powershell
kubectl delete node miami
```

Result:
- Node immediately disappears from `kubectl get nodes`
- Pods previously running on the node are rescheduled
- Cluster behaves as if the node was scaled in

Verify:

```powershell
kubectl get nodes
```

---

## Step 3 — (Optional) Observe rescheduling behavior

You can watch pods being rescheduled:

```powershell
kubectl get pods -A -o wide
```

This closely mimics autoscaler-driven scale-in behavior.

---

## Step 4 — Re-add the node to the cluster

SSH back into the same node and restart kubelet:

```powershell
sudo systemctl start kubelet
```

Result:
- kubelet re-registers with the control plane
- Node automatically reappears with the same name
- No `kubeadm join` required

Verify:

```powershell
kubectl get nodes
```

The node should now be `Ready` again.

---

## Key Characteristics of This Method

- ✅ Fully reversible
- ✅ No kubeadm reset or rejoin
- ✅ No certificate regeneration
- ✅ No cluster downtime
- ✅ Realistic simulation of node scale-in / scale-out

---

## When NOT to Use This

Avoid this approach if:
- You are permanently decommissioning hardware
- You want to rotate node certificates
- You are changing node identity (hostname, IP, role)

For those cases, a proper `kubeadm reset` is more appropriate.

---

## Summary

Stopping kubelet + deleting the Node object is the **cleanest and safest way** to temporarily remove a node from a kubeadm-managed cluster while preserving the ability to instantly add it back.

