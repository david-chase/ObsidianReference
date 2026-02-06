#k8s #nodes #pods #configuration

```table-of-contents
```
## Why 110 is the default

- By default a node is granted a pod CIDR with a mask of /24
- This grants a maximum of 256 IP addresses per node.

### Why 110 instead of 256

While a /24 subnet provides 256 addresses, Kubernetes does not allow the pod count to equal the total address count for several reasons:

1. Infrastructure Overhead: Addresses are reserved for the node's own interface, the bridge, and potentially other networking services.
2. Collision Avoidance: During rolling updates or pod restarts, new pods are often created before the old pods are fully terminated. This requires a buffer of available IPs to ensure the new pods can bind to an address immediately.
3. Resource Stability: Limiting the pod density prevents a single node from being overwhelmed by the management overhead of hundreds of concurrent containers, such as the volume of iptables rules or CNI attachment operations.

The number 110 was selected as a safe, conservative default that provides roughly a 2-to-1 ratio of available IPs to pods, ensuring that even during high-churn events, the node will not run out of IP addresses.

---
## Set MaxPodsPerNode on bare metal

This guide explains how to change the **maximum number of Pods per node** on a Kubernetes bare metal cluster configured with `kubeadm`, using the `kubelet` config file.

### Step-by-Step Instructions

1. **Open the kubelet configuration file**:

   ```sh
   sudo nano /var/lib/kubelet/config.yaml
   ```

2. **Add or modify the following line**:

   ```yaml
   maxPods: 250
   ```

   Replace `250` with your desired maximum number of pods.

3. **Save and close the file**, then restart the kubelet to apply the changes:

   ```sh
   sudo systemctl restart kubelet
   ```

4. **Verify the change**:

   ```sh
   kubectl describe node <your-node-name> | grep Pods
   ```

   You should see the new maximum pod count reflected.

### Notes

- This method is preferred when using `kubeadm`, as it aligns with how kubelet is managed through `config.yaml`.
- The change is node-specific and must be applied to each node where you want a higher pod limit.
- Ensure your node has sufficient CPU and memory to support the increased number of pods.
