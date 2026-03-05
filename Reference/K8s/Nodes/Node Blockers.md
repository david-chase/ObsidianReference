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

## Local storage -- Not unevictable

- If a cluster uses the Rancher local path storage provider (or an equivalent) or uses hostPath volumes for storage, this storage is tied to the node on which the pod is running.  This renders the pod unevictable.

## Special Cases
### IP Exhaustion

- Every pod in a cluster must have a unique IP.  If your cluster CIDR is too small, you may exhaust your available IPs and no longer be able to create pods.
- While this isn't node blocking in the traditional sense, it's still a related issue as it blocks new pod creation