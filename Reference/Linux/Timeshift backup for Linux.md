#havana #backup #linux #tech #timeshift

Installation:
``` powershell
sudo add-apt-repository universe
sudo apt update
sudo apt install timeshift
```

Do a full backup:

``` powershell
sudo timeshift --create --comments "Manual backup" --tags D
```

And then an incremental one:

```
sudo timeshift --create --comments "Manual incremental backup" --tags D
```

```
Selected default snapshot type: RSYNC
Mounted '/dev/nvme0n1p2' at '/run/timeshift/10946/backup'
Selected default snapshot device: /dev/nvme0n1p2
------------------------------------------------------------------------------
Estimating system size...
Creating new snapshot...(RSYNC)
Saving to device: /dev/nvme0n1p2, mounted at path: /run/timeshift/10946/backup
Syncing files with rsync...
Created control file: /run/timeshift/10946/backup/timeshift/snapshots/2025-04-21_17-05-08/info.json
RSYNC Snapshot saved successfully (78s)
Tagged snapshot '2025-04-21_17-05-08': ondemand
------------------------------------------------------------------------------
```

Run this to list all snapshots:

```
sudo timeshift --list
```

See the size of all snapshots:

```
sudo du -sh /timeshift/snapshots/*
```

Restore a backup:

```
sudo timeshift --restore
```

## Restore from Live USB (if system won’t boot)

1. Boot from an Ubuntu Live USB.
2. Open a terminal and install Timeshift (if not preinstalled):
    `sudo apt-add-repository -y ppa:teejee2008/ppa sudo apt update sudo apt install timeshift`
3. Launch it:
    `sudo timeshift-launcher`
4. Choose your snapshot and restore as usual.

> ☝️ Timeshift will detect and mount your installed system, even from the live environment.

Config file is at `/etc/timeshift/timeshift.json`:

```
{
  "backup_device_uuid" : "5a53db3a-2317-439f-b1ed-4a06fe28315a",
  "parent_device_uuid" : "",
  "do_first_run" : "false",
  "btrfs_mode" : "false",
  "include_btrfs_home_for_backup" : "false",
  "include_btrfs_home_for_restore" : "false",
  "stop_cron_emails" : "true",
  "schedule_monthly" : "false",
  "schedule_weekly" : "false",
  "schedule_daily" : "false",
  "schedule_hourly" : "false",
  "schedule_boot" : "false",
  "count_monthly" : "2",
  "count_weekly" : "3",
  "count_daily" : "5",
  "count_hourly" : "6",
  "count_boot" : "5",
  "date_format" : "%Y-%m-%d %H:%M:%S",
  "exclude" : [
    "**/home/dchase/Downloads",
    "**/home/dchase/.cache",
    "**/home/dchase/Videos",
    "**/home/dchase/dev",
    "**/home/dchase/gitrepos"
  ],
  "exclude-apps" : []
}
```