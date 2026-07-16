---
date: '2026-07-16T21:01:33+02:00'
draft: true
title: 'Default Path'
---
## Where is my stuff?

- `$SNAP_DATA` (`/var/snap/nextcloud/current/` by default)
    - Logs (Apache, PHP, MySQL, Redis, and Nextcloud logs)
    - Keys and certificates
    - MySQL database
    - Redis database
    - Nextcloud config
    - Any Nextcloud apps installed by the user
- `$SNAP_COMMON` (`/var/snap/nextcloud/common/` by default)
    - Nextcloud data

## Permissions and confinement?

[Snap confinement](https://snapcraft.io/docs/snap-confinement) is an **Ubuntu security feature**. This is the reason [Nextcloud snap is not supported on non-**Ubuntu** distributions](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Why-Ubuntu-is-the-only-supported-distro). So unless you allow the Nextcloud snap to access [removable media](https://github.com/nextcloud-snap/nextcloud-snap#removable-media) in `/mnt` or `/media` you will not be able to access any other directory outside of the confinement on Ubuntu.

[Removable media](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media) **must** be mounted to either `/media` or `/mnt` as **root** with **root** permissions and **connected** to Snap!

The interface providing the ability to access removable media is not automatically connected upon install, so if you'd like to use external storage (or otherwise use a device in /media or /mnt for data), you need to give the snap permission to access removable media by connecting that interface:
```bash
sudo snap connect nextcloud:removable-media
```
----