# Mount and Unmount USB Drive on Ubuntu

**Date:** 2025-04-22

## Mounting a USB Drive

To mount a USB drive located at `/dev/sdb1` to `/mnt/usb`:

```powershell
sudo mkdir -p /mnt/usb
sudo mount /dev/sdb1 /mnt/usb
```

### Additional Notes:
- Check existing mounts:
  ```powershell
  lsblk
  ```
- For NTFS or exFAT support, install required packages:
  ```powershell
  sudo apt install ntfs-3g exfat-fuse
  ```

- Verify mount:
  ```powershell
  df -h | grep /mnt/usb
  ```

## Unmounting the USB Drive

To unmount the USB drive:

```powershell
sudo umount /mnt/usb
```

Or using the device path:

```powershell
sudo umount /dev/sdb1
```

### Optional: Flush write buffers before unmounting

```powershell
sync
```

This ensures all data is written to disk before removal.

