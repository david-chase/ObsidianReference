#nfs #storage #k8s 

**Date:** 2025-04-11

## Summary

This document provides step-by-step instructions for setting up an NFS-backed dynamic storage provisioner in a Kubernetes cluster.

---

## Prerequisites

- A working Kubernetes cluster (e.g., created with kubeadm)
- An accessible NFS server
- NFS share directory with proper permissions

Example NFS Server:
- **IP:** 192.168.1.100
- **Path:** /srv/nfs/k8s

---

## Steps

### 1. Create Namespace for NFS Provisioner

```sh
kubectl create namespace nfs-storage
```

### 2. Install the NFS Client Provisioner

Save the following YAML as `nfs-client-provisioner.yaml` and apply it:

```yaml
# Contains service account, RBAC, and deployment
# Replaces NFS_SERVER and NFS_PATH with your environment values
...
```

Apply it:

```sh
kubectl apply -f nfs-client-provisioner.yaml
```

### 3. Create a StorageClass

Save this as `nfs-storageclass.yaml`:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-storage
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: example.com/nfs
reclaimPolicy: Retain
volumeBindingMode: Immediate
```

Apply it:

```sh
kubectl apply -f nfs-storageclass.yaml
```

### 4. Deploy an nginx Pod with PVC

Save as `nginx-nfs-deployment.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-nfs-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  storageClassName: nfs-storage
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          volumeMounts:
            - name: nfs-volume
              mountPath: /usr/share/nginx/html
      volumes:
        - name: nfs-volume
          persistentVolumeClaim:
            claimName: nginx-nfs-pvc
```

Apply it:

```sh
kubectl apply -f nginx-nfs-deployment.yaml
```

---

## Validation

```sh
kubectl get pvc
kubectl get pods
kubectl describe pvc nginx-nfs-pvc
```

Use `kubectl exec` to check if `/usr/share/nginx/html` is mounted correctly.

---

