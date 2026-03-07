#k8s #nodes 

```table-of-contents
```

## Differences Between nodeSelector and nodeAffinity

In Kubernetes, both **nodeSelector** and **nodeAffinity** are used to constrain which nodes a pod can be scheduled on based on labels. However, they differ significantly in flexibility and expressive power.

### nodeSelector

The **nodeSelector** is the simplest form of node selection constraint. It is a field in the pod specification that contains a map of key-value pairs. For a pod to be eligible to run on a node, the node must have each of the indicated labels.

- **Simplicity**: It is easy to use but only supports a "must match" (AND) logic.
- **Exact Match**: It only supports equality-based requirements.
- **Hard Constraint**: If no node matches the labels exactly, the pod will remain in a **Pending** state.

Example in a pod manifest:

YAML

```
nodeSelector:
  disktype: ssd
```

### nodeAffinity

**nodeAffinity** is a more robust scheduling feature that expands on the capabilities of **nodeSelector**. It allows for more complex logic and "soft" preferences.

- **Logical Operators**: It supports operators beyond equality, such as **In**, **NotIn**, **Exists**, **DoesNotExist**, **Gt** (greater than), and **Lt** (less than).
- **Hard and Soft Rules**: It provides two types of constraints:
    - **RequiredDuringSchedulingIgnoredDuringExecution**: A hard limit (similar to nodeSelector). The pod will not be scheduled unless the rule is met.
    - **PreferredDuringSchedulingIgnoredDuringExecution**: A soft limit. The scheduler will try to find a node that meets the rule, but if none are available, it will still schedule the pod on a different node.
- **Weighting**: You can assign a **weight** (1-100) to preferred rules to tell the scheduler which preferences are more important when multiple nodes are available.

---

## Adding Worker Nodes

``` bash
swapoff -a
apt update
apt install -y gnupg
apt install -y wget apt-transport-https software-properties-common
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | gpg --dearmor -o /etc/apt/trusted.gpg.d/microsoft.gpg
install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/
rm microsoft.gpg
echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/24.04/prod noble main" > /etc/apt/sources.list.d/microsoft-powershell.list
apt update
apt install -y powershell
pwsh
```

``` powershell
# 1. Update package index
apt update

# 2. Install required dependencies
apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates lsb-release

# 3. Add Docker's official GPG key
mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 4. Set up Docker’s apt repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. Update package index again (with Docker repo)
apt update

# 6. Install containerd
apt install -y containerd.io

# 7. Configure containerd and start the service
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml
```

Install kubeadm etc:

``` powershell
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | tee /etc/apt/sources.list.d/kubernetes.list
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

On the host computer:

```
kubeadm token create --print-join-command
```

Back in the docker worker node:

```
mount -t bpf bpffs /sys/fs/bpf
kubeadm join 192.168.1.100:6443 --token g810cc.vropghw5mmm9esav --discovery-token-ca-cert-hash sha256:00ca0198e6f675874b348768c4f570d73cbe3f0282fe96a4c8f17f50b63b2689
```

---