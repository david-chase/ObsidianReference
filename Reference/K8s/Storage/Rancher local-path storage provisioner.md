#standard #storage #pv #pvc


```table-of-contents
```
## Where the local-path provisioner stores its files

By default, the **Rancher Local Path Provisioner** stores the contents of PersistentVolumes under:

``` text
/opt/local-path-provisioner
```

Each PersistentVolume will have its own subdirectory inside that path, usually named after the PVC or PV.

### Example Structure:

``` text
/opt/local-path-provisioner/pvc-<uid>/
```

Where `<uid>` is the UID of the PVC object.

---
## Deployment
### Prerequisites

- A working Kubernetes cluster (e.g. kubeadm-based)
- `kubectl` access to the cluster
- At least one worker node
- `/opt/local-path-provisioner` or similar directory available on the worker nodes (this will be used to store PV data)

### Step-by-step Deployment Instructions

#### 1. **Install the Local Path Provisioner**

Run the following command to apply the default manifest maintained by Rancher:

```powershell
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
```

This deploys:

- The `local-path-provisioner` pod in its own namespace (`local-path-storage`)
- A `StorageClass` named `local-path`

#### 2. **(Optional) Mark It as Default StorageClass**

Check current default storage class:

```powershell
kubectl get storageclass
```

If `local-path` is **not** marked `(default)`, patch it:

```powershell
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

#### 3. **Verify Deployment**

Check that the pod is running:

```powershell
kubectl -n local-path-storage get all
```

Check the StorageClass:

```powershell
kubectl get storageclass
```

You should see `local-path` and it should be marked as the default.

#### 4. **Test It with a PVC**

Create a test PVC and pod:

```yaml
# test-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: test-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-path
  resources:
    requests:
      storage: 1Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
    - name: test-container
      image: busybox
      command: ["sleep", "3600"]
      volumeMounts:
        - mountPath: "/data"
          name: test-storage
  volumes:
    - name: test-storage
      persistentVolumeClaim:
        claimName: test-pvc
```

Apply it:

```powershell
kubectl apply -f test-pvc.yaml
```

Check the pod and PVC:

```powershell
kubectl get pvc
kubectl get pod test-pod
```

#### 5. **Cleanup (Optional)**

```powershell
kubectl delete -f test-pvc.yaml
```
