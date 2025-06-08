#linux #maintenance 

## 🖥️ Installing and Running Cockpit on Ubuntu 24.04

### 🔧 Install:


``` powershell
sudo apt update 
sudo apt install cockpit cockpit-system
```

(Optionally add `cockpit-podman`, `cockpit-networkmanager`, or `cockpit-storaged` for extra modules.)

### ▶️ Enable and Start:

``` powershell
sudo systemctl enable --now cockpit.socket
```

### 🌐 Access:

Go to `https://<your-server-ip>:9090`  
Log in with your normal Linux user (must be sudo-capable to see all features)