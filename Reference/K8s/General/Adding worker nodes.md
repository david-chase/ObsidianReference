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
apt install kmod
kubeadm join 192.168.1.100:6443 --token g810cc.vropghw5mmm9esav --discovery-token-ca-cert-hash sha256:00ca0198e6f675874b348768c4f570d73cbe3f0282fe96a4c8f17f50b63b2689
```