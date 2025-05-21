#ingress #nginx 

# Install nginx

``` powershell
# 1. Create the ingress-nginx namespace
kubectl create namespace ingress-nginx

# 2. Deploy the NGINX Ingress Controller via manifest
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.9.0/deploy/static/provider/cloud/deploy.yaml

# 3. (Optional) Install via Helm if you prefer
# helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
# helm repo update
# helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace

# 4. Verify the controller is running
kubectl -n ingress-nginx get pods
```

