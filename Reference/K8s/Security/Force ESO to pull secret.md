#eso #k8s #security #secrets

``` powershell
kubectl annotate externalsecret <secretname> `
  -n <namespace> `
  "reconcile.eso.external-secrets.io/requested-at=$(Get-Date -Format o)"
```

``` powershell
kubectl annotate externalsecret ecr-creds `
  -n densify `
  "reconcile.eso.external-secrets.io/requested-at=$(Get-Date -Format o)"
```
