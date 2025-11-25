#apt #software #update #upgrade #linux 

```table-of-contents
```
### Updating Package Lists

`sudo apt update`

Refreshes the list of available packages and versions.

### Upgrading Installed Packages

`sudo apt upgrade`

Upgrades installed packages to the latest version **without removing anything**.

`sudo apt full-upgrade`

Upgrades packages **and allows removing/replacing** if necessary (safer after major upgrades).

### Installing Packages

`sudo apt install <package>`

Examples:

`sudo apt install htop sudo apt install build-essential`

### Removing Packages

`sudo apt remove <package>`

Removes package but **keeps** configuration files.

`sudo apt purge <package>`

Removes package **and** configuration files.

### Searching for Packages

`apt search <keyword>`

Example:

`apt search docker`

### Viewing Package Info

`apt show <package>`

Example:

`apt show firefox`

### Cleaning Up Package Cache

`sudo apt autoremove`

Removes unused dependencies.

`sudo apt clean`

Clears downloaded package files from `/var/cache/apt/archives`.

`sudo apt autoclean`

Removes **old** cached packages.

### Listing Installed Packages

`apt list --installed`

### Checking for Upgradeable Packages

`apt list --upgradeable`

### Downloading a Package Without Installing

`apt download <package>`

### Fixing Broken Dependencies

`sudo apt --fix-broken install`