#laptop #power #ubuntu #linux #suspend 

**Date:** 2025-04-24

This guide explains how to configure Ubuntu 24.04 so your laptop:

1. Does **not power down or sleep** when the lid is closed
2. **Never sleeps or hibernates** while plugged in

---

## 🔧 1. Prevent sleep or power-down on lid close

Ubuntu uses **logind** from systemd to handle lid switch behavior.

### Steps:

Edit the logind configuration:

```bash
sudo nano /etc/systemd/logind.conf
```

Find and modify these lines:

```
HandleLidSwitch=ignore
HandleLidSwitchDocked=ignore
```

Then restart logind:

```bash
sudo systemctl restart systemd-logind
```

---

## 🔋 2. Prevent suspend or hibernation when plugged in

You can use either the GUI or terminal.

### GUI Method:

- Go to **Settings → Power**
- Set **Automatic Suspend**:
  - **Plugged In**: Off
  - **On Battery**: Your preference
- Set **Blank Screen** to "Never" if desired

### Terminal Method:

Use `gsettings`:

```bash
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-timeout 0
```

---

## 💡 Optional: Disable Hibernation Entirely

Check available power states:

```bash
cat /sys/power/state
```

To disable all sleep modes:

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

To re-enable:

```bash
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

---

## ✅ Summary

- Lid close behavior is controlled via `/etc/systemd/logind.conf`
- Sleep on AC power can be managed via Settings or `gsettings`
- Optional masking of system sleep services disables suspend entirely
