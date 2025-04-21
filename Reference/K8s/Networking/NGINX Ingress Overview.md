#nginx #ingress #k8s #networking #chatgpt

**Date:** 2025-04-11

---

## 🔧 How does an NGINX Ingress work in Kubernetes?

An NGINX Ingress Controller is a reverse proxy running inside your Kubernetes cluster. It routes incoming external traffic (usually HTTP/HTTPS) to internal services based on routing rules you define in Ingress resources.

### Basic Workflow:
1. **Deploy Ingress Controller** (like NGINX).
2. **Create Ingress Resources** to define routing rules.
3. **Ingress Controller** watches for these rules and routes traffic accordingly.
4. **Expose the Controller** via LoadBalancer, NodePort, or in Minikube via `minikube tunnel`.

---

## 🌐 Can I access multiple services from outside my cluster?

**Yes!** An Ingress allows access to multiple services through:
- One IP and port (usually 80/443).
- Routing rules based on paths or hostnames.

### Example:

| Path         | Routed to Service       |
|--------------|--------------------------|
| `/`          | `web-app-service`        |
| `/api`       | `api-service`            |
| `/dashboard` | `dashboard-service`      |

---

## 🧭 How does NGINX know which service to direct my traffic to?

Through Kubernetes **Ingress resources** — here's how:

1. You define path/host-based routing in a YAML file.
2. NGINX watches Kubernetes for changes and updates its config.
3. Incoming requests are matched to rules.
4. NGINX reverse proxies to the appropriate Kubernetes service.

### Example Ingress YAML:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.local
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

---

## 🧪 In Minikube

To use NGINX Ingress in Minikube:

```powershell
minikube addons enable ingress
minikube tunnel
```

Then access services via:

```
http://<minikube-ip>/api
http://<minikube-ip>/web
```

Or configure `/etc/hosts` for host-based routing.

---
