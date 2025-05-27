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
```
