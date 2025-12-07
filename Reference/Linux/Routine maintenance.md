#linux #maintenance

### Upgrade firmware(s)

``` powershell
sudo fwupdmgr refresh
sudo fwupdmgr get-updates
sudo fwupdmgr update
```

### Update all apt packages

``` powershell
sudo apt update
sudo apt upgrade
```

### Update Ubuntu and drivers

``` PowerShell
sudo apt update
sudo apt full-upgrade -y
sudo ubuntu-drivers install
sudo update-initramfs -u
# sudo reboot
```
### Remove unused packages

``` powershell
sudo apt autoremove --purge -y
sudo apt clean
```

### Update distro

``` powershell
sudo apt full-upgrade -y
```

### Clean up journal logs**

To prevent `/var/log/journal` from growing large:

``` powershell
sudo journalctl --vacuum-time=2weeks
```

### Reboot