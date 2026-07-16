---
date: '2026-07-16T21:02:04+02:00'
draft: true
title: 'Managing Nextcloud Snap With Snap'
---
<h1 align="center">Managing Nextcloud snap with Snap</h1>

Since [snaps](https://ubuntu.com/core/services/guide/snaps-intro) are Ubuntu-centric, its no surprise that Nextcloud snap was designed to run on Ubuntu. The folks at [Canonical](https://ubuntu.com/community/governance/canonical) have made it really easy to [install Ubuntu](https://ubuntu.com/download/server) on almost any hardware out there.

Snapd is a secure and robust technology which the Nextcloud snap team has embraced. Combining Nextcloud with [snapd](https://ubuntu.com/core/services/guide/snaps-intro) makes it a perfect fit for IoT or scalable environments. The Nextcloud snap team packages a stable default upstream Nextcloud, adds some snap magic, tests and releases that snap package.

[![Get it from the Snap Store](https://snapcraft.io/static/images/badges/en/snap-store-white.svg)](https://snapcraft.io/nextcloud)

## Install snap
```
sudo apt update && sudo apt install snapd
```

## Snap manual pages
```
man snap
```

## Snap help
```
snap --help
```
![grafik](https://user-images.githubusercontent.com/54933878/235139733-f2cc0b60-eebc-491a-8265-999506ffd75c.png)

### Contextual help
```
snap <command> --help
```
also
```
snap <command> <option> --help
```

**Documentation and further reading:**

+ [Introduction to snaps](https://ubuntu.com/core/services/guide/snaps-intro)

+ [Snap Documentation](https://snapcraft.io/docs)

+ [Nextcloud snap on Ubuntu](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Why-Ubuntu-is-the-only-supported-distro)

## View installed enabled snaps on host
List enabled snaps on host
```
sudo snap list
```
![grafik](https://user-images.githubusercontent.com/54933878/235135007-1a0b8e50-1718-4c71-a243-f7994bff86f4.png)

## View all snaps on host
List **all** snaps and revisions on host
```
sudo snap list --all
```
![grafik](https://user-images.githubusercontent.com/54933878/235135305-75853fd3-dc2c-4439-bb74-81cd01dfa1ae.png)

## Enable Nextcloud snap
Enable Nextcloud snap to start automatically
```
sudo snap enable nextcloud
```

## Disable Nextcloud snap
Disable Nextcloud snap from starting automatically
```
sudo snap disable nextcloud
```

## Start Nextcloud snap
Start Nextcloud snap manually
```
sudo snap start nextcloud
```

## Stop Nextcloud snap
Stop Nextcloud snap manually
```
sudo snap stop nextcloud
```

## Restart Nextcloud snap
Restart Nextcloud snap manually
```
sudo snap restart nextcloud
```

## Managing automatic updates
+ [Snapcraft documentation](https://snapcraft.io/docs/managing-updates)

Nextcloud snap is designed to update (refresh) automatically by default. 

[**You have full control**](https://snapcraft.io/docs/managing-updates) to manage [**if** and **when**](https://snapcraft.io/docs/managing-updates) your Nextcloud snap is updated with snapd daemon! See [managing updates](https://snapcraft.io/docs/managing-updates).

> [!NOTE]
> Snaps update automatically, and by default, the snapd daemon checks for updates 4 times a day. Each update check is called a refresh.
> Updates can be set to occur on Friday at midnight, or for specific days of the month, such as only the third Monday, or even the last Friday of the month, between 23:00 to 01:00 the next day. They can even be postponed indefinitely, or for a set period of time.
> When, if, and how often, these updates occur is the job of the snap refresh command.


## Update Nextcloud snap
Update Nextcloud snap manually
```
sudo snap refresh nextcloud
```

## Revert Nextcloud snap 
Revert Nextcloud snap to **last** retained revision
```
sudo snap revert nextcloud
```

## Revert Nextcloud snap revision
Revert Nextcloud snap to **defined** revision
```
sudo snap revert nextcloud --revision=xxxxx
```
![grafik](https://user-images.githubusercontent.com/54933878/235136149-6d90b10c-cbfd-45c2-9470-aff8b13ef904.png)

## Refresh to stable channel 
[Release strategy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Release-strategy)

Refresh to stable channel

```
sudo snap refresh nextcloud --stable
```

or defined [channel](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Release-strategy)

```
sudo snap refresh --channel=25/stable nextcloud
```

----

# Snap snapshots
[Snap snapshots documentation](https://snapcraft.io/docs/snapshots)

## Functions

![grafik](https://user-images.githubusercontent.com/54933878/235142637-6aecf2d1-52eb-44b8-a934-797f59a1231e.png)


## Backup Nextcloud snap using snap snapshots

### Backup procedure

 1. **Stop Nextcloud** (optional but recommended)
```
sudo snap stop nextcloud
```
 2. **Create snapshot**
```
sudo snap save nextcloud
```
 3. **Start Nextcloud** (see 1.)
``` 
sudo snap start nextcloud
```
 4. **Find snapshot** (`*.zip`) in `/var/lib/snapd/snapshots` and copy/move to backup media

The above procedure may be scripted ([example script](https://github.com/scubamuc/scubamuc.github.io/blob/scubamuc/wiki-md/NEXTCLOUD.snap--backup_snap-snapshot.md))

## Restore Nextcloud snap using snap snapshots 

> * When moving to new device, be sure to install Nextcloud snap first
> * Snap restore replaces previous installation incl. certs, DB and data
> * See documentation in `snap restore --help` and [snapcraft](https://snapcraft.io/docs/snapshots#heading--restoring)

### Restore procedure

1. **Copy snapshot** (`*.zip`) from backup media to `/var/lib/snapd/snapshots`

2. **Discover snapshot-ID** using `snap saved`

3. **Restore snapshot** 
```
sudo snap restore "snapshot-ID"
```
## Snap change in progress! 

discover the snap change in progress:

```
snap changes
```

example output:
```
ID    Status  Spawn                   Ready                   Summary
1426  Done    yesterday at 17:26 CET  yesterday at 17:26 CET  Auto-refresh snap "curl"
1427  Doing   yesterday at 19:03 CET  today at 00:55 CET      <some process name>  "nextcloud"
```
The process ID is 1427 in the above example has status "Doing" and looks like its taking too long (you'll have a different process ID)
 
abort the process
```
sudo snap abort <ID>
```

## Remove snap snapshot

```
sudo snap forget "snapshot-ID"
```

## Snap retention

Use snap option `refresh.retain` to set the maximum number of a snap revisions stored by the system **after** the next refresh:

Value = Number of revisions retained
```
sudo snap set system refresh.retain=2
```

## Remove disabled snap revisions

* see [Snap retention](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-Nextcloud-snap-with-Snap/_edit#snap-retention)! depending on retention, `disabled` snaps will be removed automatically.

**Remove disabled snap revision manually**
* check all snaps on your system `snap list --all | grep 'disabled'`
   * be aware that the value `disabled` may differ depending on your locale
* remove disabled snap revision `sudo snap remove <snapname> --revision <revision>`

**Example script to remove all disabled snap revisions**

>This will run `snap list -all` and extract the lines that contain the word `disabled`. This will be different depending on your locale, so check the output of the function first, then update `awk '/disabled/` to replace `disabled` with the label that is found in your output. 
>Save the script to a file (for example `scrub-snaps.sh`) and then set it as being executable: `sudo chmod +x scrub-snaps.sh`

```bash
#!/bin/bash
# This script will remove disabled snap revisions.
set -eu

LANG=C snap list --all | awk '/disabled/{print $1, $3}' |
    while read name rev; do
        snap remove "$name" --revision="$rev"
    done
```
>
>Now you can run it, remembering to use sudo: `sudo ./scrub-snaps.sh`

----

# Remove Nextcloud snap from host
This will remove Nextcloud snap from your system but will create a snapshot 
```
sudo snap remove nextcloud
```

# Remove Nextcloud snap completely from host
This will completely remove Nextcloud snap including revisions
```
sudo snap remove --purge nextcloud
```
> [!NOTE]
> #### Removing Nextcloud snap from host
> * The command `sudo snap remove nextcloud` will automatically create a snapshot (incl. the *default* data directory) of Nextcloud snap before removing it from your host. This will include data which is present in the *default* data directory see [what’s inside a snapshot](https://snapcraft.io/docs/snapshots#heading--anatomy).
>   * you can remove that snapshot later by issuing `sudo snap forget <snapshot-ID>` if you're happy
> * The `--purge` option `sudo snap remove --purge nextcloud` will remove Nextcloud snap from the host (incl. *default* data directory) **without** creating a snapshot
>
> Regardless which command is used to remove Nextcloud snap from your host, data in a *non-default* **external** directory will remain untouched as that is not part of the snap.
> 
> See [what’s inside a snapshot](https://snapcraft.io/docs/snapshots#heading--anatomy) and [default path](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Default-path) to find your stuff.
----
# Remove snapd from host

> [!WARNING]
>
> Be sure you want to remove snapd completely
>
> **This is final! All snaps, revisions, backups and settings will be removed** 

> [!CAUTION]
> This will completely remove snapd including all installed enabled/disabled snaps and all revisions from your system 
```
sudo apt remove snapd
```
