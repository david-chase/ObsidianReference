#usb #linux #format #partition

This guide explains how to identify, confirm, and format a USB stick on a Linux system.

---

## 1. Identify the USB Device

After inserting the USB stick, run:

```
lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT,LABEL,MODEL,TRAN
```

Look for a device with `TRAN` = `usb`, such as `/dev/sdb`.

Example output:
```
NAME   FSTYPE   SIZE MOUNTPOINT LABEL MODEL            TRAN
sda              1T                      Samsung SSD     sata
├─sda1 ext4     512M /boot
└─sda2 ext4     999G /
sdb             14.9G                  USB Flash Drive  usb
└─sdb1 vfat     14.9G /media/usb
```

---

## 2. Confirm It’s a USB Drive

Run either of the following:

```
lsblk -d -o NAME,MODEL,TRAN | grep usb
```

Or:

```
udevadm info --query=all --name=/dev/sdX | grep ID_BUS
```

Replace `/dev/sdX` with the correct device name.

---

## 3. Wipe and Format the USB Drive

**⚠️ Warning: These commands are destructive. Double-check the device name!**

Assuming the USB stick is `/dev/sdb`:

### a. Wipe existing partitions

```
sudo wipefs -a /dev/sdb
```

### b. (Optional) Zero out the beginning of the disk

```
sudo dd if=/dev/zero of=/dev/sdb bs=1M count=100
```

### c. Create a new partition table and partition

```
sudo parted /dev/sdb --script mklabel msdos mkpart primary ext3 0% 100%
```

### d. Format the partition as ext3

```
sudo mkfs.ext3 /dev/sdb1
```

---

Let me know if you need steps for labeling, auto-mounting, or making it bootable.
