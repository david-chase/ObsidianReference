#k8s #gke #autopilot #google 

```table-of-contents
```

## General Notes

- While Prometheus *can* be deployed to GKE Autopilot I suspect most or all customers will use Google Managed Prometheus, which is enabled by default.
- You can use pod affinity to schedule pods together, but based on other pod labels or topology keys like region or zone.  But you can't do it based on host.
- GKE Autopilot does bin packing, GKE Standard does not
- Anthos doesn't do bin packing because you're managing your own nodes
- You're charged for <span style="font-style:italic; color:rgb(0, 130, 0)">requests, not limits or utilization</span>.  However if your utilization exceeds requests your pod could be throttled or evicted.
- There's <span style="color:rgb(0, 130, 0)">little reason to set limits on pods</span> in GKE Autopilot since you're not paying for bursting and GKE enforces its own upper limits.  You just run the risk of running out of resources.
- GKE prevents you from arbitrarily low requests by setting minimum requests for each resource type and enforcing CPU:Memory ratios
- <span style="color:rgb(255, 0, 0)">This means in GKE Autopilot it would be very important for Kubex to know and respect these minimums and ratios</span> 

## Minimum and Maximum Requests

[https://cloud.google.com/kubernetes-engine/pricing#general-purpose-autopilot-workloads](https://docs.cloud.google.com/kubernetes-engine/docs/concepts/autopilot-resource-requests#compute-class-defaults)

| Compute Class   | CPU:Memory Ratio | Resource | Minimum                                                                                               | Maximum                                                                                                                                                                                           |
| --------------- | ---------------- | -------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| General Purpose | 1:1 to 1:6.5     | CPU      | - **Clusters that support bursting**: 50m CPU<br>- **Clusters that don't support bursting**: 250m CPU | 30 vCPU                                                                                                                                                                                           |
|                 |                  | Memory   | - **Clusters that support bursting**: 52 MiB<br>- **Clusters that don't support bursting**: 512 MiB   | 110 GiB                                                                                                                                                                                           |
| Balanced        | 1:1 to 1:8       | CPU      | 0.25 vCPU                                                                                             | 222 vCPU<br><br>If [minimum CPU platform](https://docs.cloud.google.com/kubernetes-engine/docs/how-to/min-cpu-platform) selected:<br><br>- Intel platforms: 126 vCPU<br>- AMD platforms: 222 vCPU |
|                 |                  | Memory   | 0.5 GiB                                                                                               | 851 GiB<br><br>If [minimum CPU platform](https://docs.cloud.google.com/kubernetes-engine/docs/how-to/min-cpu-platform) selected:<br><br>- Intel platforms: 823 GiB<br>- AMD platforms: 851 GiB    |
| Performance     | n/a              | CPU      | No minimum requests enforced                                                                          | See link                                                                                                                                                                                          |
|                 |                  | Memory   | No minimum requests enforced                                                                          | See link                                                                                                                                                                                          |

## Bursting Capability

The following types of Pods can burst in any GKE version that supports the hardware that the Pods request:

- [Pods that request GPUs](https://docs.cloud.google.com/kubernetes-engine/docs/how-to/autopilot-gpus)
- [Pods that request a specific machine series](https://docs.cloud.google.com/kubernetes-engine/docs/how-to/performance-pods)

For all other types of Pods, bursting becomes available when you restart the control plane after ensuring that the cluster meets all of the following conditions:

- The cluster is running `cgroupv2`. Clusters created with GKE version 1.26 or later, or have migrated to `cgroupv2` will meet this condition. See [check the cgroup mode](https://docs.cloud.google.com/kubernetes-engine/docs/how-to/migrate-cgroupv2#check-cgroup-mode) to determine the current cgroup version, and migrate if needed.
- The cluster is running GKE version 1.30.2-gke.1394000 or later.

- If your cluster supports bursting, Autopilot doesn't enforce 0.25 vCPU increments for your Pod CPU requests. If your cluster doesn't support bursting, Autopilot rounds up your CPU requests to the nearest 0.25 vCPU. To check whether your cluster supports bursting, see [Bursting availability in GKE](https://docs.cloud.google.com/kubernetes-engine/docs/how-to/pod-bursting-gke#availability-in-gke).

## Default Requests

- If no requests are specified, GKE Autopilot defaults to 500m CPU and 2GiB Memory.

## Compute Classes

- The default compute class is General Purpose

### Implementation Example

To request a specific compute class for a deployment, you would include the `cloud.google.com/compute-class` label in your manifest:

``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: high-performance-app
spec:
  replicas: 3
  template:
    spec:
      nodeSelector:
        cloud.google.com/compute-class: Performance
      containers:
      - name: web-app
        image: my-app-image
```