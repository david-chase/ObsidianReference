#argocd #application #finalizer

``` powershell
kubectl patch app ingress-nginx -n argocd -p '{"metadata":{"finalizers":[]}}' --type=merge
```
