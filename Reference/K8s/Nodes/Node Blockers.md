#nodes #k8s #autoscaling 

```table-of-contents
```

There are a number of conditions that can block nodes from scaling down when they otherwise should:

## Scale-down Blockers
### Nodes exceeding Max Pods Per Node

- When a node exceeds its Max Pods per Node, it can no longer accept new pods no matter how many resources it has available.  A new node will be scaled out, resulting in underutilized nodes.

### Pod Disruption Budgets & Pod Anti-affinity

- If you're using PDBs to maintain service availability, there will be a certain number of pods that must always be running to meet that PDB.  
- Pod anti-affinities can be used to reduce blast radius by forcing pods to be spread across multiple nodes.  Particularly when combined with PDBs this can cause "X" number of nodes to always exist to meet your availability and anti-affinity requirements.

## Topology Spread Constraints

- Similar to PDBs and pod anti-affinity, Pod Topology Spread Constraints force pods to be spread across regions for latency and availability reasons.  
- This can force nodes to be created within these regions, that may not necessarily have other workloads running on them.

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

## Local storage

- If a cluster uses the Rancher local path storage provider (or an equivalent) or uses hostPath volumes for storage, this storage is tied to the note on which the pod is running.  This can cause the pod to be tied to the node, meaning the node cannot be scaled down.

## Special Cases
### IP Exhaustion

- Every pod in a cluster must have a unique IP.  If your cluster CIDR is too small, you may exhaust your available IPs and no longer be able to create pods.
- While this isn't node blocking in the traditional sense, it's still a related issue as it blocks new pod creation