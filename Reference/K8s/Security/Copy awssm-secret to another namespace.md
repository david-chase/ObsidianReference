#eso #secret #aws

``` powershell
kubectl get secret awssm-secret -n external-secrets -o yaml |
ForEach-Object { $_ -replace 'namespace: external-secrets', 'namespace: <namespace>' } | kubectl apply -f -
```