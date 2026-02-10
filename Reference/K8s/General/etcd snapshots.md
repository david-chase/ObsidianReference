#k8s #maintenance #backup #etcd

This document summarizes **how to take, store, and restore etcd snapshots** in a kubeadm-based Kubernetes cluster with a **local etcd static pod**.

---
## Assumptions

- Kubernetes installed with **kubeadm**
- Single control plane node
- etcd running as a **static pod**
- Standard kubeadm paths:
  - `/etc/kubernetes/manifests/etcd.yaml`
  - `/etc/kubernetes/pki/etcd/`

---
## 1. Take an etcd Snapshot

Run **on the control plane node**.

```powershell
sudo ETCDCTL_API=3 etcdctl snapshot save /var/backups/etcd-snapshot.db `
  --endpoints=https://127.0.0.1:2379 `
  --cacert=/etc/kubernetes/pki/etcd/ca.crt `
  --cert=/etc/kubernetes/pki/etcd/server.crt `
  --key=/etc/kubernetes/pki/etcd/server.key
```

Expected output:
```text
Snapshot saved at /var/backups/etcd-snapshot.db
```

---
## 2. Verify the Snapshot

Always verify before trusting a snapshot.

```powershell
sudo ETCDCTL_API=3 etcdctl snapshot status /var/backups/etcd-snapshot.db
```

You should see revision, key count, and size information.

---
## 3. Copy the Snapshot to Another Node

Snapshots **must not live only on the control plane disk**.

Example: copy to a worker node.

```powershell
scp /var/backups/etcd-snapshot.db miami:/srv/backups/
```

You may also copy to:
- NAS
- External disk
- Off-host backup system

---
## 4. Restore an etcd Snapshot (Disaster Recovery)

⚠️ This procedure **replaces the entire cluster state**.

### 4.1 Stop the Control Plane

```powershell
sudo systemctl stop kubelet
```

This stops all static pods, including etcd and the API server.

---
### 4.2 Restore the Snapshot to a New Data Directory

```powershell
sudo ETCDCTL_API=3 etcdctl snapshot restore /var/backups/etcd-snapshot.db `
  --data-dir=/var/lib/etcd-restored
```

---
### 4.3 Replace the Existing etcd Data

```powershell
sudo mv /var/lib/etcd /var/lib/etcd.broken
sudo mv /var/lib/etcd-restored /var/lib/etcd
```

Ensure ownership is correct:

```powershell
sudo chown -R root:root /var/lib/etcd
```

---
### 4.4 Restart the Control Plane

```powershell
sudo systemctl start kubelet
```

The kubelet will:
- Restart etcd
- Restart kube-apiserver
- Bring controllers back online

---
## 5. Post-Restore Validation

Check node and pod health:

```powershell
kubectl get nodes
kubectl get pods -A
```

Confirm cluster behaves as expected.

---
## Notes & Best Practices

- etcd snapshots are **application-consistent** and safe
- Filesystem snapshots alone (e.g., Timeshift) are **not sufficient**
- Always store snapshots **off-node**
- Practice restores before you need them
- Automation (systemd timer or CronJob) can be added later

---
## Summary

| Task | Tool |
|----|----|
| OS rollback | Timeshift |
| Cluster state backup | etcd snapshot |
| Logical recovery | etcd restore |

This approach provides a **safe, kubeadm-supported recovery path**.
