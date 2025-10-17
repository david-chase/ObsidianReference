#security #k8s #secrets #operator 

```table-of-contents
```

## ESO and AWS credentials

When using a ClusterSecretStore you have to create the secret with your AWS credentials in every namespace where you'll be deploying an ExternalSecret.

``` powershell
# Copy the secret from the external-secrets namespace into another
kubectl get secret awssm-secret -n external-secrets -o yaml |
ForEach-Object { $_ -replace 'namespace: external-secrets', 'namespace: <namespace>' } | kubectl apply -f -
```


## Force ESO to pull secret

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
