#argo #argocd #k8s #cli 

``` powershell
# Set the current context to ArgoCD namespace
kubectl config set-context --current --namespace=argocd

# Create an app
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default

# See status of an app
argocd app get guestbook

# Sync an app
argocd app sync guestbook

# Delete an application
k delete application <appname> -n argocd

# Delete an applicationset
k delete applicationset <setname> -n argocd

# List applicationsets
k get applicationset -n argocd

# List applications
k get application -n argocd

# Get the initial admin password - not necessarily current
kubectl get secret argocd-initial-admin-secret `
  -n argocd `
  -o jsonpath="{.data.password}" | `
  %{ [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($_)) }

```
