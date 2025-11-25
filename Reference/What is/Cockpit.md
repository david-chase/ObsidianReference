#linux #maintenance 

```table-of-contents
```

https://cockpit-project.org/
## What is Cockpit?

Cockpit is a **web-based, lightweight system administration interface** for Linux servers. It provides a graphical dashboard for managing system settings, containers, storage, networking, users, services, and logs—without replacing the underlying CLI tools. Everything it does is reflected in the system’s real configuration files and systemd units, so nothing is hidden or proprietary.

## Installing and Running Cockpit on Ubuntu 24.04

### 🔧 Install:


``` powershell
sudo apt update 
sudo apt install cockpit cockpit-system
```

(Optionally add `cockpit-podman`, `cockpit-networkmanager`, or `cockpit-storaged` for extra modules.)

### Enable and Start:

``` powershell
sudo systemctl enable --now cockpit.socket
```

### Access:

Go to `https://<your-server-ip>:9090`  
Log in with your normal Linux user (must be sudo-capable to see all features)