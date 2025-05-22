#k8s #nodes #pods #configuration

This guide explains how to change the **maximum number of Pods per node** on a Kubernetes bare metal cluster configured with `kubeadm`, using the `kubelet` config file.

## 🛠 Step-by-Step Instructions

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

## Notes

- This method is preferred when using `kubeadm`, as it aligns with how kubelet is managed through `config.yaml`.
- The change is node-specific and must be applied to each node where you want a higher pod limit.
- Ensure your node has sufficient CPU and memory to support the increased number of pods.
