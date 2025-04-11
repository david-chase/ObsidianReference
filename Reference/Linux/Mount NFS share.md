#linux #nfs 


This guide explains how to connect to an NFS share from **Ubuntu Desktop 24.04**, using PowerShell-style commands.

---

## 🧰 Step 1: Install NFS Client

```powershell
sudo apt update
sudo apt install -y nfs-common
```

---

## 📂 Step 2: Create a Mount Point

```powershell
sudo mkdir -p /mnt/nfs_share
```

---

## 🌐 Step 3: Mount the NFS Share Temporarily

```powershell
sudo mount -t nfs 192.168.1.100:/srv/nfs/shared /mnt/nfs_share
```

- Replace `<NFS-SERVER-IP>` with the IP address of your NFS server.
- Replace `/path/to/share` with the exported NFS directory path.

**Example:**

```powershell
sudo mount -t nfs 192.168.1.100:/export/data /mnt/nfs_share
```

---

## 🔄 Step 4 (Optional): Mount Automatically at Boot

Edit the `/etc/fstab` file:

```powershell
sudo nano /etc/fstab
```

Add this line:

```
192.168.1.100:/export/data  /mnt/nfs_share  nfs  defaults  0  0
```

Then apply it:

```powershell
sudo mount -a
```

---

## 🧪 Step 5: Verify the Mount

```powershell
df -h | grep nfs
```

---

