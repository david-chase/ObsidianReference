#nodes #k8s #autoscaling 

```table-of-contents
```

There are a number of conditions that can block nodes from scaling down when they otherwise should:

## Scale-down Blockers

The following scenarios cause the node on which a workload is running to be ignored by Karpenter or Cluster Autoscaler when making node consolidation or scale-down decisions.  This effectively renders an individual node blocked from scaling down.

### Unmanaged Pods

- Karpenter and cluster autoscaler will not attempt to scale down nodes running "Naked", stand-alone, static, or unmanaged pods.  Examples include:
	- Pods deployed from a manifest with "Kind: Pod"
	- Pods deployed using `kubectl run
	- Pods created by placing a manifest in the `/etc/kubernetes/manifests/` folder of a node
- In theory if you had 5 static pods in your cluster, each placed on different nodes, then your Kubernetes cluster could never downscale to fewer than 5 nodes, no matter how low their utilization.
- For this and other reasons it's generally not considered a best practice to deploy unmanaged pods in Kubernetes.

### Deployments at their PDB threshold

- Imagine a scenario where you have a deployment that consists of 3 replicas, and a Pod Disruption Budget that specifies that 3 copies of the workload must be running at all times.  This, in effect, makes all the pods unevictable since restarting even one of them would violate the PDB.
- Since the pods cannot be evicted, the nodes on which they're running can never be scaled down.

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: nginx-pdb
spec:
  minAvailable: 3
  selector:
    matchLabels:
      app: nginx
```

### hostPath and EmptyDir storage

- Pods that use hostPath volumes become tied to that node because the data they're using exists only on that node.  Karpenter and Cluster Autoscaler will avoid scaling down any nodes with these types of storage on them as it will likely result in data loss.

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-hostpath
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: local-data
          mountPath: /usr/share/nginx/html
      volumes:
      - name: local-data
        hostPath:
          path: /data/nginx
          type: DirectoryOrCreate
```

- Similarly, Karpenter and Cluster Autoscaler will generally not scale down a node with pods that use emptyDir storage, since this storage is also tied to the node on which the pod is running, and will likely result in data loss if terminated.

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-emptydir
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: scratch-space
          mountPath: /tmp/cache
      volumes:
      - name: scratch-space
        emptyDir: {}
```
### Local storage

- If a cluster uses the Rancher local path storage provider (or an equivalent) for persistent storage, the resulting PV will be bound to the node on which it's created using nodeAffinity.  As long as the pod bound to that PV are running, the node on which the PV was created cannot be scaled down.

``` YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-path
  resources:
    requests:
      storage: 1Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-local
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: storage
          mountPath: /data
      volumes:
      - name: storage
        persistentVolumeClaim:
          claimName: nginx-pvc
```

### "Do Not Evict" Annotations

- If a pod has the annotation `"cluster-autoscaler.kubernetes.io/safe-to-evict": "false"` (cluster autoscaler) or `"karpenter.sh/do-not-disrupt": "true"` (Karpenter) then it will never be evicted to do a node consolidation.
- This prevents the node on which it's running from ever being scaled down.

``` YAML
apiVersion: v1
kind: Pod
metadata:
  name: nginx-protected
  annotations:
    cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
    karpenter.sh/do-not-disrupt: "true"
spec:
  containers:
  - name: nginx
    image: nginx
```

### Pods in Terminating or Unknown State, Stuck Finalizers

- When a node is being considered for scale-down, autoscalers must ensure that all workloads can be safely evicted or have already exited. A pod stuck in a **Terminating** or **Unknown** state will prevent the node the pod is running on from being scaled down.
- Pods with stuck finalizers are a common example of this scenario.  Pods may be stuck in a Terminating state indefinitely, perhaps without your being aware.  This can block a node from scale-down indefinitely.
- Use this command to identify pods stuck in this state:

``` bash
kubectl get pods -A | grep -E 'Terminating|Unknown'
```

## Disk Pressure

- When a node's root filesystem or image filesystem crosses a high-usage threshold (typically **85%** by default), the `kubelet` posts a **DiskPressure** condition.
- Once this condition is active, the node is automatically **tainted** with `node.kubernetes.io/disk-pressure:NoSchedule`.
- The kubernetes scheduler will skip any node with this taint when trying to place new pods.

## Node Sprawl

While not directly blocking an individual node from being scaled down, the following scenarios prevent the *total nodes* in a cluster or node group from dropping below a certain level.  

### Nodes exceeding Max Pods Per Node

- When a node exceeds its Max Pods per Node, it can no longer accept new pods no matter how many resources it has available.  A new node will be scaled out, resulting in underutilized nodes.
- Use this command to list all the nodes in your cluster including the Max Pods Per Node. 

``` bash
kubectl get nodes -o custom-columns="NODE:.metadata.name,MAX_PODS:.status.allocatable.pods"
```

- Cross-reference this with the output of the following command, which shows a count of pods per node:

``` bash
kubectl get pods -A -o jsonpath='{range .items[*]}{.spec.nodeName}{"\n"}{end}' | sort | uniq -c
```

- If you are at or near your maximum on any nodes, consider increasing the Max Pods Per Node on that or all nodes, or spreading small pods evenly across many nodes rather than letting them concentrate on a single node.

### Topology Spread Constraints

- Pod topology spread constraints are used to resolve latency and availability issues by forcing pods to be spread across multiple physical regions.  
- Take, for example, the manifest below.  This creates a Deployment with 3 replicas, but all these replicas will be scheduled in different regions.  This means the Deployment will always require 3 nodes (in different regions) -- they cannot be scheduled on a single node.

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-topology
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: topology.kubernetes.io/region
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: nginx
      containers:
      - name: nginx
        image: nginx
```

- In most cases this is an intentional architectural decision that trades lower latency and fault tolerance for additional cost.

### Ephemeral Storage Requests

- Containers can request ephemeral storage in much the same way as they can request memory and CPU.   
- If a node has insufficient storage available to satisfy the ephemeral storage requests, it will be filtered out during node selection.
- This could cause additional nodes to scale out to meet all ephemeral storage requests, even if there's sufficient node capacity to satisfy CPU and memory requests.

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: storage-example
spec:
  replicas: 1
  selector:
    matchLabels:
      app: storage-app
  template:
    metadata:
      labels:
        app: storage-app
    spec:
      containers:
      - name: app-container
        image: nginx
        resources:
          requests:
            ephemeral-storage: "2Gi"
          limits:
            ephemeral-storage: "4Gi"
```

### Node Affinity or Anti-affinity, Node Selectors

- Node affinity is an "attraction" rule.  It tells the scheduler: "place this pod on a node that meets these criteria."  Node anti-affinity, on the other hand, is a "repulsion" rule. It explicitly tells the scheduler: "Do not place this pod on a node that already meets these criteria." 
- Node constraints can be either soft (constraint begins with "`preferred...`") or hard (constraint begins with "`required...`")
- When using hard node affinity constraints, the Kubernetes scheduler will refuse to place a pod if no nodes satisfy the constraint.  The pod will instead be placed in Pending status.
- Your node autoscaler will then detect the pending pod and attempt to scale out a node that meets its scheduling constraints.
- Here is an example of a Deployment manifest with a hard node anti-affinity.  If no node exists **without** a diskType of "hdd" a new node will need to be scaled out.

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
spec:
  selector:
    matchLabels:
      app: web-server
  template:
    metadata:
      labels:
        app: web-server
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: disktype
                operator: NotIn
                values:
                - hdd
      containers:
      - name: nginx
        image: nginx
```

- A `nodeSelector` in a pod spec is similar to a node affinity except that it is simpler.  Node selectors only match node labels, and constraints are always hard.

### Pod anti-affinity

- Pod anti-affinity rules tell the Kubernetes scheduler "do not place this pod on a node that already has pods that meet these criteria".  
- Like node anti-affinity, pod anti-affinity constraints can be either soft (constraint begins with "`preferred...`") or hard (constraint begins with "`required...`")
- When using hard anti-affinity constraints, the Kubernetes scheduler will refuse to place a pod if no nodes satisfy the constraint.  The pod will instead be placed in Pending status.
- Your node autoscaler will then detect the pending pod and attempt to scale out a node that meets its scheduling constraints.
- Below is an example of a Deployment manifest requesting 3 replicas, with a hard anti-affinity rule.  The rule says, in effect, "don't place any of the pods in this Deployment on the same node."  As a result, this Deployment will require at least 3 nodes already exist (or be scaled out) to satisfy its placement rules:

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-server
  template:
    metadata:
      labels:
        app: web-server
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web-server
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx
        image: nginx
```

### Daemonsets

## Special Cases
### IP Exhaustion

- Every pod in a cluster must have a unique IP.  If your cluster CIDR is too small, you may exhaust your available IPs and no longer be able to create pods.
- While this isn't node blocking in the traditional sense, it's still a related issue as it blocks new pod creation