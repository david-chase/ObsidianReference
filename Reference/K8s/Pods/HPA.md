#pods #k8s #autoscaling #resources 

```table-of-contents
```

## Dangers of scaling on CPU only

- If you set CPU requests too low, you will scale out way too many replicas and waste memory
- If you set CPU requests too high, you may never scale out, even while running out of memory
- Using KEDA to scale on queues and actual business events will always be more robust than scaling on CPU or memory alone

## Leave replicas unspecified when using HPA

When using the **Horizontal Pod Autoscaler (HPA)** in Kubernetes, it's generally advisable to **omit the `replicas` field** in your **Deployment**, **ReplicaSet**, or **StatefulSet** manifest. Instead, you should define the **minReplicas** and **maxReplicas** in your HPA configuration.

### Why?

1. **Avoid Conflicts**: If you set `replicas` in the Deployment manifest and HPA is enabled, Kubernetes may encounter conflicts when scaling pods.
2. **HPA Takes Control**: HPA dynamically adjusts the number of replicas based on CPU/memory or custom metrics, making a static `replicas` field redundant.
3. **Declarative Configuration**: By leaving `replicas` out, you ensure that HPA fully controls scaling, preventing accidental overrides by applying an outdated Deployment manifest.

### Best Practice

- **Leave `replicas` unspecified** in the Deployment manifest.
- **Define `minReplicas` and `maxReplicas` in the HPA resource.**

#### Example

**Deployment.yaml (No `replicas`)**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app-image
```

**HPA.yaml (Scaling Control)**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```
