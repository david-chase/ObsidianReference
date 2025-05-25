#krew #kubectl #k8s #cli 

``` powershell
# From an admin shell
choco install krew

# From a user shell
kubectl krew index remove default --force
kubectl krew index add default https://github.com/kubernetes-sigs/krew-index.git
kubectl krew update

# Find plugins
kubectl krew search

# Show installed plugins
kubectl krew list
```