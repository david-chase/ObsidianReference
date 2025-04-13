``` bash
swapoff -a
apt update
apt install -y gnupg
apt install -y wget apt-transport-https software-properties-common
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | gpg --dearmor -o /etc/apt/trusted.gpg.d/microsoft.gpg
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
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu `
$(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. Update package index again (with Docker repo)
apt update

# 6. Install containerd
apt install -y containerd.io

# 7. Configure containerd and start the service
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml

# 8. Enable and restart containerd
systemctl enable containerd
systemctl restart containerd

# 9. Verify it's running
systemctl status containerd

```