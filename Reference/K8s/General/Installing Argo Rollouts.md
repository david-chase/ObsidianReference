#rollouts #argo #deployment #k8s

``` powershell
k create ns argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/download/v1.6.4/install.yaml
```

#### Installing the kubectl cli plugin

1. Download the plugin from https://github.com/argoproj/argo-rollouts/releases/latest/

2. Rename it to `kubectl-argo-rollouts.exe`

3. Move it somewhere in the path