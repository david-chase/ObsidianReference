#kibana #service #ingress #minikube

## Step-by-Step: Expose Kibana via Ingress

### 1. ✅ Enable Ingress in Minikube

`minikube addons enable ingress`

This deploys the NGINX ingress controller into your Minikube cluster.

---

### 2. 📝 Create an Ingress Resource for Kibana

Assuming Kibana is deployed in the `logging` namespace with the Helm release name `kibana`, it will have a service like `kibana.logging.svc.cluster.local`.

Create an ingress resource file: `kibana-ingress.yaml`

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kibana-ingress
  namespace: logging
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: kibana.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: kibana
                port:
                  number: 5601

```

Apply it:

`kubectl apply -f kibana-ingress.yaml`

---

### 3. 🧪 Update Your Local Hosts File (for DNS)

You need to route `kibana.local` to your Minikube IP:

`minikube ip`

Add an entry like this to your **hosts file** (`C:\Windows\System32\drivers\etc\hosts` on Windows):

`192.168.49.2 kibana.local`

_(Replace `192.168.49.2` with your actual Minikube IP.)_

---

### 4. 🌍 Access Kibana

Now go to your browser and open:

`http://kibana.local`

You should see the Kibana UI.