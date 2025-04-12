# 🚀 Deploy Prometheus and kube-state-metrics on GKE Autopilot

## 📦 Deploy Prometheus on GKE Autopilot

### 1. Create Namespace
```powershell
kubectl create namespace monitoring
```

### 2. Create RBAC and ServiceAccount
**File: `prometheus-rbac.yaml`**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
  namespace: monitoring
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: prometheus
rules:
  - apiGroups: [""]
    resources: [nodes, nodes/metrics, services, endpoints, pods]
    verbs: ["get", "list", "watch"]
  - apiGroups: ["extensions", "networking.k8s.io"]
    resources: [ingresses]
    verbs: ["get", "list", "watch"]
  - nonResourceURLs: ["/metrics"]
    verbs: ["get"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
  - kind: ServiceAccount
    name: prometheus
    namespace: monitoring
```

### 3. Create Prometheus ConfigMap
**File: `prometheus-config.yaml`**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s

    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']

      - job_name: 'kube-state-metrics'
        static_configs:
          - targets: ['kube-state-metrics.monitoring.svc.cluster.local:8080']
```

### 4. Deploy Prometheus StatefulSet
**File: `prometheus-statefulset.yaml`**
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: prometheus
  namespace: monitoring
spec:
  serviceName: "prometheus"
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      serviceAccountName: prometheus
      securityContext:
        runAsUser: 0
      containers:
        - name: prometheus
          image: prom/prometheus:v2.52.0
          args:
            - "--config.file=/etc/prometheus/prometheus.yml"
            - "--storage.tsdb.path=/prometheus"
          ports:
            - containerPort: 9090
              name: web
          volumeMounts:
            - name: config-volume
              mountPath: /etc/prometheus
            - name: storage-volume
              mountPath: /prometheus
      volumes:
        - name: config-volume
          configMap:
            name: prometheus-config
  volumeClaimTemplates:
    - metadata:
        name: storage-volume
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 10Gi
```

### 5. Create Prometheus Service
**File: `prometheus-service.yaml`**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus
  namespace: monitoring
spec:
  type: ClusterIP
  ports:
    - port: 9090
      targetPort: web
      protocol: TCP
      name: http
  selector:
    app: prometheus
```

---

## 📊 Deploy kube-state-metrics on GKE Autopilot

### 1. Create RBAC and ServiceAccount
**File: `kube-state-metrics-rbac.yaml`**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: kube-state-metrics
  namespace: monitoring
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: kube-state-metrics
rules:
  - apiGroups: [""]
    resources:
      - configmaps
      - secrets
      - nodes
      - pods
      - services
      - resourcequotas
      - replicationcontrollers
      - limitranges
      - persistentvolumeclaims
      - persistentvolumes
      - namespaces
    verbs: ["list", "watch"]
  - apiGroups: ["apps"]
    resources: [statefulsets, daemonsets, deployments, replicasets]
    verbs: ["list", "watch"]
  - apiGroups: ["batch"]
    resources: [cronjobs, jobs]
    verbs: ["list", "watch"]
  - apiGroups: ["autoscaling"]
    resources: [horizontalpodautoscalers]
    verbs: ["list", "watch"]
  - apiGroups: ["policy"]
    resources: [poddisruptionbudgets]
    verbs: ["list", "watch"]
  - apiGroups: ["storage.k8s.io"]
    resources: [storageclasses, volumeattachments]
    verbs: ["list", "watch"]
  - apiGroups: ["coordination.k8s.io"]
    resources: [leases]
    verbs: ["list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kube-state-metrics
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: kube-state-metrics
subjects:
  - kind: ServiceAccount
    name: kube-state-metrics
    namespace: monitoring
```

### 2. Deploy kube-state-metrics
**File: `kube-state-metrics-deployment.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kube-state-metrics
  namespace: monitoring
  labels:
    app: kube-state-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kube-state-metrics
  template:
    metadata:
      labels:
        app: kube-state-metrics
    spec:
      serviceAccountName: kube-state-metrics
      containers:
        - name: kube-state-metrics
          image: registry.k8s.io/kube-state-metrics/kube-state-metrics:v2.10.1
          ports:
            - containerPort: 8080
              name: http-metrics
            - containerPort: 8081
              name: telemetry
```

### 3. Create the Service
**File: `kube-state-metrics-service.yaml`**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: kube-state-metrics
  namespace: monitoring
  labels:
    app: kube-state-metrics
spec:
  ports:
    - name: http-metrics
      port: 8080
      targetPort: http-metrics
  selector:
    app: kube-state-metrics
  type: ClusterIP
```

---

### ✅ Verify
Access Prometheus at:  
http://localhost:9090/targets  
(After running `kubectl port-forward svc/prometheus 9090:9090 -n monitoring`)
