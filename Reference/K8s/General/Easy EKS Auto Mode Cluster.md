#aws #eks #automode #howto #tutorial 

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


## Installing Argo CD

``` powershell
kubectl create namespace argocd
```
 
``` powershell
kubectl apply -n argocd `
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Wait for the deployment to finish

``` powershell
kubectl wait --for=condition=Available `
  deployment/argocd-server `
  -n argocd `
  --timeout=300s
```

Add support for Kustomize+Helm charts

``` powershell
kubectl patch configmap argocd-cm `
  -n argocd `
  --type merge `
  -p '{"data":{"kustomize.buildOptions":"--enable-helm"}}'
```

``` powershell
kubectl rollout restart deployment argocd-repo-server -n argocd
```

Get the admin password

``` powershell
kubectl get secret argocd-initial-admin-secret `
  -n argocd `
  -o jsonpath="{.data.password}" | `
  %{ [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($_)) }
```

Login to the local UI

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then connect to https://localhost:8080

Go to User Info --> Update Password