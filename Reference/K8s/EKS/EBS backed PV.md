#storage #eks #k8s #ebs #automode

As suspected, creating EBS-backed PVs in EKS is an absolute breeze.

1. Deploy the EBS CSI driver add-on
2. Create an EBS CSI backed storageclass

``` yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
parameters:
  type: gp3
```

3. Create a PVC.  Below is a test deployment with PVC

``` yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: ebs-sc
  resources:
    requests:
      storage: 5Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: ebs-app
spec:
  containers:
    - name: app
      image: busybox
      command: ["sleep", "3600"]
      volumeMounts:
        - mountPath: "/data"
          name: ebs-volume
  volumes:
    - name: ebs-volume
      persistentVolumeClaim:
        claimName: ebs-claim
```

- OMG FFS the entire time the only problem with EKS Auto mode was the provisioner name in the storageclass definition is "ebs.csi.eks.amazonaws.com"

#### StorageClass definition for EKS Auto Mode

``` yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: ebs.csi.eks.amazonaws.com
volumeBindingMode: WaitForFirstConsumer
parameters:
  type: gp3
```