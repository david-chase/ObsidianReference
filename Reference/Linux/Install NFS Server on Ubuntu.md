#linux #ubuntu #nfs

**Snapshot created on:** 2025-04-11 13:05:53

---

## ✅ Step-by-Step: Install and Configure NFS Server on Ubuntu 24.04

### 1. Install the NFS Server Package

```bash
sudo apt update
sudo apt install nfs-kernel-server -y
```

---

### 2. Create a Directory to Share

```bash
sudo mkdir -p /srv/nfs/shared
sudo chown nobody:nogroup /srv/nfs/shared
sudo chmod 755 /srv/nfs/shared
```

---

### 3. Configure the NFS Exports

```bash
sudo nano /etc/exports
```

Add:

```
/srv/nfs/shared 192.168.1.0/24(rw,sync,no_subtree_check)
```

> Replace `192.168.1.0/24` with your client's subnet.

---

### 4. Export the Shares

```bash
sudo exportfs -a
```

---

### 5. Enable and Start the NFS Service

```bash
sudo systemctl enable nfs-server
sudo systemctl start nfs-server
sudo systemctl status nfs-server
```

---

### 6. (Optional) Allow NFS Through the Firewall

```bash
sudo ufw allow from 192.168.1.0/24 to any port nfs
sudo ufw enable
```

---

### 7. Verify the Exported Share

```bash
sudo exportfs -v
```

---
