#nodes #k8s #autoscaling 

```table-of-contents
```
## Introduction

Many of us understand the concept of Kubernetes Requests and Limits, and that by reducing over-sized resource requests we can reduce waste in our clusters.  And for GKE Autopilot and EKS Fargate clusters that is true.  Because you're being billed directly for the resources you're requesting, driving down requests can result in instantaneous savings.

However in most hosted Kubernetes environments you're not actually being billed for requests.  Instead you're being billed for the **node infrastructure**.  We trust that by reducing wasteful resource requests our node autoscaler (typically Karpenter or Cluster Autoscaler) will be smart enough to do a node scale down.  However this is not always the case.

Without understanding how node autoscalers work, you could be stuck optimizing all those requests and limits only to find your node infrastructure hasn't shrunk at all, or barely so.  This article will walk through the many scenarios that can impact the size of your node infrastructure and help you avoid scale-down blockers that can cost your company millions.  

For the purposes of this article I will use the term "node autoscalers" to mean Karpenter and Cluster Autoscaler.  

## Scale-down Blockers

The following scenarios cause the node on which a workload is running to be ignored by the node autoscaler when making node consolidation or scale-down decisions.  This effectively renders an individual node blocked from scaling down.  If one or two nodes in your cluster are affected by these scale-down blockers the impact may be minimal, but if dozens or hundreds of nodes are impacted, you could be paying for huge amounts of under-utilized infrastructure.

### Unmanaged Pods

Node autoscalers will not attempt to scale down nodes running "Naked", stand-alone, static, or unmanaged pods.  Examples include:

- Pods deployed from a manifest with "`Kind: Pod`"
- Pods deployed using `kubectl run
- Pods created by placing a manifest in the `/etc/kubernetes/manifests/` folder of a node

In theory if you had 5 static pods in your cluster, each placed on different nodes, then your Kubernetes cluster could never downscale to fewer than 5 nodes, no matter how low their utilization.  For this and other reasons you should generally avoid deploying unmanaged pods in Kubernetes.

### hostPath and emptyDir Storage

Pods that use hostPath volumes become tied to that node because the data they're using exists only on that node.  Node autoscalers will avoid scaling down any nodes with these types of volumes on them as it will likely result in data loss.

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

Similarly, node autoscalers will generally not scale down a node with pods that use emptyDir storage, since this storage is also tied to the node on which the pod is running, and will likely result in data loss if terminated.

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

### Local Storage

If a cluster uses the Rancher local path storage provider (or an equivalent) for persistent storage, the resulting PV will be bound to the node on which it's created using nodeAffinity.  As long as the pods bound to that PV are running, the node on which the PV was created cannot be scaled down.

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

If a pod has the annotation `"cluster-autoscaler.kubernetes.io/safe-to-evict": "false"` (cluster autoscaler) or `"karpenter.sh/do-not-disrupt": "true"` (Karpenter) then it will never be evicted to do a node consolidation.  This prevents the node on which it's running from ever being scaled down.

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

When a node is being considered for scale-down, autoscalers must ensure that all workloads can be safely evicted or have already exited. A pod stuck in a **Terminating** or **Unknown** state will prevent the node the pod is running on from being scaled down.

Pods with stuck finalizers are a common example of this scenario.  Pods may be stuck in a Terminating state indefinitely, perhaps without your being aware.  This can block a node from scale-down indefinitely.  Use this command to identify pods stuck in this state:

``` bash
kubectl get pods -A | grep -E 'Terminating|Unknown'
```

### Deployments at their PDB threshold

Imagine a scenario where you have a deployment that consists of 3 replicas, and a Pod Disruption Budget that specifies that 3 copies of the workload must be running at all times.  This, in effect, makes all the pods unevictable since restarting even one of them would violate the PDB.  Since the pods cannot be evicted, the nodes on which they're running can never be scaled down.

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

## Node Sprawl

While not directly blocking an individual node from being scaled down, the following scenarios either inflate the **total nodes** in a cluster or node group or prevent them from dropping below a certain level.  When these nodes go underutilized it's known as node sprawl.

### Disk Pressure

When a node's root filesystem or image filesystem crosses a high-usage threshold (typically **85%** by default), the `kubelet` posts a **DiskPressure** condition.   Once this condition is active, the node is automatically **tainted** with `node.kubernetes.io/disk-pressure:NoSchedule`.  The kubernetes scheduler will skip any node with this taint when trying to place new pods, often resulting in new nodes being needed.

### Nodes exceeding Max Pods Per Node

When a node exceeds its Max Pods per Node, it can no longer accept new pods no matter how many resources it has available.  A new node will be scaled out, resulting in underutilized nodes.  Use this command to list all the nodes in your cluster including the Max Pods Per Node. 

``` bash
kubectl get nodes -o custom-columns="NODE:.metadata.name,MAX_PODS:.status.allocatable.pods"
```

Cross-reference this with the output of the following command, which shows a count of pods per node:

``` bash
kubectl get pods -A -o jsonpath='{range .items[*]}{.spec.nodeName}{"\n"}{end}' | sort | uniq -c
```

If you are at or near your maximum on any nodes, consider increasing the Max Pods Per Node on that or all nodes, or spreading small pods evenly across many nodes rather than letting them concentrate on a single node.

While it's outside the scope of this article, nodes hitting their Max Pods Per Node is a form of IP exhaustion and could be resolved by increasing the size of your Node CIDR Mask.

### Topology Spread Constraints

Pod topology spread constraints are used to resolve latency and availability issues by forcing pods to be spread across multiple physical regions.  

Take, for example, the manifest below.  This creates a deployment with 3 replicas, but all these replicas will be scheduled in different regions.  This means the deployment will always require 3 nodes (in different regions) -- they cannot be scheduled on a single node.

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

In most cases this is an intentional architectural decision that trades lower latency and fault tolerance for additional cost.

### Ephemeral Storage Requests

Containers can request ephemeral storage in much the same way they can request memory and CPU.   If a node has insufficient storage available to satisfy the ephemeral storage requests, it will be filtered out during node selection.  This could cause additional nodes to scale out to meet ephemeral storage requests, even if there's sufficient node capacity to satisfy CPU and memory requests.

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

While this is no different than needing to scale out a node due to exhausted CPU or Memory resources, ephemeral storage is often overlooked as a finite resource that must be managed.

### Node Affinity or Anti-affinity, Node Selectors

Node affinity is an "attraction" rule.  It tells the scheduler: "place this pod on a node that meets these criteria."  Node anti-affinity, on the other hand, is a "repulsion" rule. It explicitly tells the scheduler: "Do not place this pod on a node that already meets these criteria." 

Node constraints can be either soft (constraint begins with "`preferred...`") or hard (constraint begins with "`required...`")  When using hard node affinity constraints, the Kubernetes scheduler will refuse to place a pod if no nodes satisfy the constraint.  The pod will instead be placed in Pending status.  Your node autoscaler will then detect the pending pod and attempt to scale out a node that meets its scheduling constraints.

Here is an example of a deployment manifest with a hard node anti-affinity.  If no node exists **without** a diskType of "hdd" a new node will need to be scaled out.

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

A `nodeSelector` in a pod spec is similar to a node affinity except that it is simpler.  Node selectors only match node labels, and constraints are always hard.

### Pod anti-affinity

Pod anti-affinity rules tell the Kubernetes scheduler "do not place this pod on a node that already has pods that meet these criteria".  Like node anti-affinity, pod anti-affinity constraints can be either soft (constraint begins with "`preferred...`") or hard (constraint begins with "`required...`")  

When using hard anti-affinity constraints, the Kubernetes scheduler will refuse to place a pod if no nodes satisfy the constraint.  The pod will instead be placed in Pending status.  Your node autoscaler will then detect the pending pod and attempt to scale out a node that meets its scheduling constraints.

Below is an example of a deployment manifest requesting 3 replicas, with a hard anti-affinity rule.  The rule says, in effect, "don't place any of the pods in this Deployment on the same node."  As a result, this deployment will require at least 3 nodes already exist (or be scaled out) to satisfy its placement rules:

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

### Pod Affinity

Pod affinity rules tell the kubernetes scheduler "try to schedule these workloads together."  While these rules are rarely the **direct** cause of a node scale out, they can indirectly cause one, as well as increase the average node size in your cluster.

As with other affinities, pod affinities can be either hard or soft.  Pods that share hard affinities must be scheduled together.  Therefore they can only be placed on nodes with sufficient capacity to host all the pods, not just one of them.  If there are no nodes with sufficient capacity, a new one must be scaled out.

This also means that if the total resources required for these grouped pods is high, Karpenter will favour large nodes over small ones when determining node shape.  

In the following example, we have:

- A deployment of 3 replicas of nginx, with an anti-affinity rule that specifies they must all be placed on different nodes.
- A deployment of 3 replicas of redis, with an affinity rule that a redis pod must always be placed on the same node as an nginx pod.
- This means a minimum of 3 nodes must be used to satisfy the placement of these pods.  
- Since the total memory requirements of every nginx + redis pair is 1,000Mi, if there aren't at least 3 nodes with 1,000Mi of free memory a scale out will be required.

``` YAML
---
# Deployment 1: Nginx (3 Replicas)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-web
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
      affinity:
        podAntiAffinity:
          # Hard rule: Nginx pods MUST NOT be on the same node
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - nginx
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx
        image: nginx
        resources:
          requests:
            memory: "300Mi" # Memory request as specified
---
# Deployment 2: Redis (3 Replicas)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-cache
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      affinity:
        podAffinity:
          # Hard rule: Redis pods MUST be on a node already hosting Nginx
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - nginx
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: redis
        image: redis
        resources:
          requests:
            memory: "700Mi" # Memory request as specified
```

### Partial Scheduling

Particularly with the rise of high-performance distributed frameworks like PyTorch, TensorFlow, or MPI, pods can have multiple cross-dependencies before they can complete their work.  If these pods are not all scheduled together, they can block expensive hardware resources like GPUs while sitting idle.

 These types of workloads require Gang Scheduling using a solution like the NVIDIA KAI Scheduler or the Kubernetes-native alpha feature Workload-Aware Scheduling.   While the topic of gang scheduling is outside the scope of this guide, keep in mind that as AI and parallel processing workloads become more and more popular in Kubernetes, pod interdependencies can continue to drive waste, underutilized nodes, and even resource deadlocks when using older scheduling capabilities that are only aware of individual pods.

## Daemonsets

While both Karpenter and Cluster Autoscaler ignore Daemonsets when making decisions about scaling down a node, keep in mind that because a Daemonset is deployed to every node, the impact of violating one of the preceding rules is that much more impactful.  

Imagine adding a do-not-evict annotation to a Daemonset.  You suddenly have an node group or cluster that are incapable of scaling down.

## Autoscaler-Specific Scenarios

### Cluster Autoscaler

If the node group’s `minSize` is > 0, Cluster Autoscaler won't go below it—even if the node is empty.

``` YAML
apiVersion: cluster.x-k8s.io/v1beta1
kind: MachineDeployment
metadata:
  name: worker-nodes
  namespace: default
  annotations:
    # This is the "minSize" lever for Cluster Autoscaler
    cluster.x-k8s.io/cluster-api-autoscaler-node-group-min-size: "5"
spec:
  replicas: 5 # Start with 5
  selector:
    matchLabels:
      cluster.x-k8s.io/cluster-name: my-cluster
  template:
    # ... standard pod template for nodes ...
```

 Furthermore Cluster Autoscaler uses a utilization threshold (default 50% for CPU and memory).  A node won’t be removed if its CPU and Memory resource requests exceed this threshold.  Daemonsets are not included in this calculation.

Lastly a `cluster-autoscaler.kubernetes.io/scale-down-disabled` annotation on nodes or pods prevents downsizing.

### Karpenter

If a node has drift (e.g., wrong AMI, taints, labels), Karpenter may keep or replace it instead of downsizing based on usage.

## Summary

In summary, reducing wasteful resource requests is only the first step in Kubernetes resource optimization.  In many cases you won't fully realize your savings until you've scaled down your node infrastructure, and there are a lot of factors that influence your ability to do so.  Hopefully this guide has helped you understand and avoid some of the hidden pitfalls when optimizing Kubernetes clusters.