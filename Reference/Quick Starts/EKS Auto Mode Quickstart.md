#aws #eks #automode #howto #tutorial #quickstart

1. Provision the cluster

2. Wait for the status to read "Active"

3. Enable these Add-ons:
	Amazon EBS CSI Driver 
	CoreDNS
	Amazon VPC CNI

4. Wait for them all to show as Active

5. Add the EBS storage class

``` yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gp3  # Name the new StorageClass as 'gp3'
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"  # Optional: make it the default StorageClass
provisioner: ebs.csi.eks.amazonaws.com
reclaimPolicy: Retain
# volumeBindingMode: WaitForFirstConsumer
volumeBindingMode: Immediate
parameters:
  type: gp3  # Use 'gp3' type instead of 'gp2'
allowedTopologies:
- matchLabelExpressions:
  - key: topology.ebs.csi.aws.com/zone
    values:
    - us-east-2a  # Replace with your cluster's AZ
```

6. Add the cluster to your kubeconfig

``` powershell
aws eks update-kubeconfig `
  --region ca-central-1 `
  --name dchase-testing `
  --profile Work
```