#linux #maintenance

### Upgrade firmware(s)

``` powershell
sudo fwupdmgr update
```

### Update all apt packages

``` powershell
sudo apt update
sudo apt upgrade
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