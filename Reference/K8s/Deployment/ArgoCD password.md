#password #argocd #k8s #deployment 

You should delete the `argocd-initial-admin-secret` from the Argo CD namespace once you changed the password. The secret serves no other purpose than to store the initially generated password in clear and can safely be deleted at any time. It will be re-created on demand by Argo CD if a new admin password must be re-generated.

### Retrieving the install-time password

``` powershell
# Retrieve ArgoCD admin password from the Kubernetes secret
$secret = kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"

# Decode from Base64 and display the password
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($secret))
```