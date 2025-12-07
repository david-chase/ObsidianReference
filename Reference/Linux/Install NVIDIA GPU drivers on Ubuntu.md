#nvidia #ubuntu #drivers #gpu #linux #k8s #dcgm

## ✅ **Step-by-Step NVIDIA CUDA Installation on Ubuntu 24.04**

### 🔧 Prerequisites

- You **must have an NVIDIA GPU**.
- **Disable Nouveau drivers** (Ubuntu’s open-source fallback driver) is often necessary but now handled better by newer packages.
- Use a clean Ubuntu 24.04 install if possible to avoid conflicts.

---

### 🚀 Step 1: Add NVIDIA CUDA Toolkit Repository

``` bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-ubuntu2404.pin sudo mv cuda-ubuntu2404.pin /etc/apt/preferences.d/cuda-repository-pin-600
```

Now add the CUDA repo and its GPG key:

``` bash
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/3bf863cc.pub sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/ /"
```

---

### 🔄 Step 2: Update and Install CUDA

``` bash
sudo apt update sudo apt install -y cuda
```

> 📝 This installs the latest supported NVIDIA driver and CUDA toolkit.

---

### 🧠 Optional: Install Only Driver (Without Toolkit)

If you only want the **NVIDIA driver** (e.g., for container workloads):

``` bash
sudo apt install -y nvidia-driver-550
```

(Replace `550` with the version appropriate for your card. Run `ubuntu-drivers devices` to see suggestions.)

---

### ⚙️ Step 3: Reboot

``` bash
sudo reboot
```

---

### ✅ Step 4: Verify Installation

After reboot, check that the driver and CUDA are installed:

``` powershell
# After reboot, check that the driver and CUDA are installed:
nvidia-smi

# Also check CUDA version:
nvcc --version
```