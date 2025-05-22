#k8s #kubeadm #baremetal #maxpodspernode #pods

To increase the number of pods a node can run (`MaxPodsPerNode`) in a Kubernetes cluster provisioned using `kubeadm`, follow the steps below.

---

### 1. Decide the New Value
Choose the new max pod count per node, for example:

```text
New value: 250
```

---

### 2. Edit kubelet Config File (Preferred Method)

Check the current kubelet config file:
```shell
cat /var/lib/kubelet/config.yaml | grep maxPods
```

If not set, open the file to edit:
```shell
nano /var/lib/kubelet/config.yaml
```

Add or modify the following line:
```yaml
maxPods: 250
```

---

### 3. Restart the Kubelet

After saving the changes:
```shell
systemctl daemon-reexec
systemctl restart kubelet
```

---

### 4. Validate the Configuration

Check the kubelet process or node description:
```shell
ps aux | grep kubelet | grep max-pods
```

Or from Kubernetes:
```shell
kubectl describe node <node-name> | grep -i pods
```

You should see an updated pod capacity.

---

### Alternative: Set via systemd Drop-in File

Some kubeadm setups use systemd drop-in overrides instead of a config file.

Edit the file:
```shell
nano /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```

Find or add the following line:
```ini
[Service]
Environment="KUBELET_EXTRA_ARGS=--max-pods=250"
```

Apply the changes:
```shell
systemctl daemon-reexec
systemctl daemon-reload
systemctl restart kubelet
```

---

### 5. Repeat for All Nodes

Make sure to repeat these steps on **each node** (control plane and worker) where you want to raise the `maxPods` setting.

---

Let me know if you'd like a script to automate this across multiple nodes.
