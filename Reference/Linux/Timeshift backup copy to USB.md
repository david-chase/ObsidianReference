#timeshift #backup #linux #tech

This guide explains how to copy your Timeshift snapshots to a USB drive using `rsync` for safe offline storage.

---

## 🪛 1. Plug in the USB and Mount It

Check your USB mount point:

```bash
lsblk
```

Assume it's mounted at:

```
/media/dave/BackupUSB
```

---

## 🧭 2. Find Timeshift Snapshot Location

Default location for rsync mode:

```
/timeshift/snapshots/
```

Verify with:

```bash
sudo timeshift --list
```

---

## 📦 3. Copy Snapshots Using rsync

<span style="color:rgb(255, 0, 0)">Make sure the USB is formatted with ext4</span>.  Preserve metadata and hard links:

```bash
sudo rsync -aAXv /timeshift/snapshots/ /mnt/usb/timeshift-backup/
```

> Make sure to include the trailing slashes.

---

## 💡 Optional: Copy Entire Timeshift Directory

```bash
sudo rsync -aAXv /timeshift/ /media/dave/BackupUSB/timeshift-backup/
```

This includes snapshots, backup metadata, and logs.

---

## 🧪 Test the Backup

Check copied snapshots:

```bash
ls /media/dave/BackupUSB/timeshift-backup/snapshots/
```

---

## 🧯 Restore From USB Later

To restore:
1. Mount the USB.
2. Run Timeshift.
3. If snapshots don't appear:
   - Set snapshot location in the GUI to USB path
   - Or symlink manually:

```bash
sudo ln -s /media/dave/BackupUSB/timeshift-backup/snapshots /timeshift/snapshots
```
