#argocd #deployment #k8s #tutorial #howto #quickstart 


``` powershell
kubectl create namespace argocd
```
 
``` powershell
kubectl apply -n argocd `
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Wait for the deployment to finish

``` powershell
kubectl wait --for=condition=Available `
  deployment/argocd-server `
  -n argocd `
  --timeout=300s
```

Add support for Kustomize+Helm charts

``` powershell
kubectl patch configmap argocd-cm `
  -n argocd `
  --type merge `
  -p '{"data":{"kustomize.buildOptions":"--enable-helm"}}'
```

``` powershell
kubectl rollout restart deployment argocd-repo-server -n argocd
```

Get the admin password

``` powershell
kubectl get secret argocd-initial-admin-secret `
  -n argocd `
  -o jsonpath="{.data.password}" | `
  %{ [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($_)) }
```

Login to the local UI

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then connect to https://localhost:8080

Go to User Info --> Update Password