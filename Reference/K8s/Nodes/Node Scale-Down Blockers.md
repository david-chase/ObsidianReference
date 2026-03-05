#nodes #k8s #autoscaling 

```table-of-contents
```

There are a number of conditions that can block nodes from scaling down when they otherwise should:

## Scale-down Blockers
### Nodes exceeding Max Pods Per Node

- When a node exceeds its Max Pods per Node, it can no longer accept new pods no matter how many resources it has available.  A new node will be scaled out, resulting in underutilized nodes.

### Deployments at their PDB threshold

- Imagine a scenario where you have a deployment that consists of 3 replicas, and a Pod Disruption Budget that specifies that 3 copies of the workload must be running at all times.  This, in effect, makes all the pods unevictable since restarting even one of them would violate the PDB.
- Since the pods are effectively unevictable, the nodes on which they're running can never be scaled down.

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

## Topology Spread Constraints

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

## Ephemeral Storage Requests

- Containers can request ephemeral storage in much the same way as they can request memory and CPU.   
- Once storage on a node is exhausted, you may see a new node scaled out, or you may see pods evicted and placed on nodes which do have more storage.
- Either way, the end result is node sprawl: the current node stops accepting new pods.

``` YAML
apiVersion: v1
kind: Pod
metadata:
  name: storage-example
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

## hostPath and EmptyDir storage

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
## Local storage

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

## Unmanaged Pods

- Karpenter and cluster autoscaler will not attempt to scale down nodes running "Naked", stand-alone, static, or unmanaged pods.  Examples include:
	- Pods deployed from a manifest with "Kind: Pod"
	- Pods deployed using `kubectl run
	- Pods created by placing a manifest in the `/etc/kubernetes/manifests/` folder of a node

## "Do Not Evict" Annotations

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
## Special Cases
### IP Exhaustion

- Every pod in a cluster must have a unique IP.  If your cluster CIDR is too small, you may exhaust your available IPs and no longer be able to create pods.
- While this isn't node blocking in the traditional sense, it's still a related issue as it blocks new pod creation