#argo #automation #k8s #workflows 

``` powershell
kubectl create namespace argo

kubectl apply -n argo -f https://github.com/argoproj/argo-workflows/releases/download/v3.6.7/install.yaml

# Disable client auth
kubectl patch deployment `
argo-server `
-n argo `
--type='json' `
-p='[{"op": "replace", "path": "/spec/template/spec/containers/0/args", "value": ["server","--auth-mode=server"]}]'
```

#### Port forwarding

``` powershell
kubectl -n argo port-forward deployment/argo-server 2746:2746
```