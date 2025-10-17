#security #k8s #certificates 

```table-of-contents
```

## Install cert-manager

``` powershell
helm upgrade --install cert-manager jetstack/cert-manager `
     --namespace cert-manager `
     --create-namespace `
     --version v1.18.2 `
     --set crds.enabled=true
```

## Install cert-manager CLI

``` powershell
go install github.com/cert-manager/cmctl/v2@latest
```

## Uninstalling cert-manager

``` powershell
helm uninstall cert-manager -n cert-manager
kubectl delete crd issuers.cert-manager.io
kubectl delete crd clusterissuers.cert-manager.io
kubectl delete crd certificates.cert-manager.io
kubectl delete crd certificaterequests.cert-manager.io
kubectl delete crd orders.acme.cert-manager.io
kubectl delete crd challenges.acme.cert-manager.io
kubectl delete namespace cert-manager 
```


