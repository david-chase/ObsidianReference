#eso #secret #aws

When using a ClusterSecretStore you have to create the secret with your AWS credentials in every namespace where you'll be deploying an ExternalSecret.

``` powershell
# Copy the secret from the external-secrets namespace into another
kubectl get secret awssm-secret -n external-secrets -o yaml |
ForEach-Object { $_ -replace 'namespace: external-secrets', 'namespace: <namespace>' } | kubectl apply -f -
```