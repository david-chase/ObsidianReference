#argo #argocd #k8s #tokyo

## Install the CLI

``` powershell
# Download the latest Argocd CLI binary
Invoke-WebRequest -Uri "https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64" -OutFile argocd-linux-amd64

# Make it executable and move into your PATH
sudo chmod 555 argocd-linux-amd64
sudo mv argocd-linux-amd64 /usr/local/bin/argocd

# Verify the installation
argocd version --client
```

## Deploy to cluster

``` powershell
kubectl create namespace argocd

# Install Argo CD core components
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Port forward the service

 - In a new PowerShell  window:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
## Login to the CLI

``` powershell
# 1. Grab the initial admin password
$initialPassword = (kubectl -n argocd get secret argocd-initial-admin-secret `
  -o jsonpath="{.data.password}" | base64 --decode)

# 2. Log in (use --insecure to skip cert checks on localhost)
argocd login localhost:8080 `
  --username admin `
  --password $initialPassword `
  --insecure
```

## Deploy the app-of-apps

``` powershell
kubectl apply -f apps/app-of-apps.yaml
```
