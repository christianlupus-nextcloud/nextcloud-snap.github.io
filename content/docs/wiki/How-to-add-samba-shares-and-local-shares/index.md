---
date: '2026-07-16T21:01:15+02:00'
draft: false
title: 'How to Add Samba Shares and Local Shares'
---
## Add network and local shares to Nextcloud snap as external storage

----

Due to [Snap confinement](https://snapcraft.io/docs/snap-confinement), adding samba shares, network shares and local shares such as NAS storage, USB storage and other external storage devices requires careful configuration.

## Procedure for mounting share on host
This procedure may be used for mounting network shares, USB devices, storage devices or local shares.

* connect removable media to snap
* install external storage app in Nextcloud
* configure share on host
* mount share directory to `/mnt` or `/media` in `fstab`  as **root** with **root** permissions
* configure external storage app

### Removable media

Removable media can be any device (e.g. external disk partition, SAMBA network, NFS network, SSHFS network etc.). The device should be available at system boot and mounted in `/etc/fstab` on host.

Note that the interface providing the ability to access removable media is not automatically connected upon Nextcloud snap installation, so if you'd like to use external storage (or otherwise use a device in `/media` or `/mnt` for data), you need to give snap permission to access removable media by connecting that interface:

```
sudo snap connect nextcloud:removable-media
```
### External storage app

Ensure that the external storage app is installed in your Nextcloud instance. Refer to your Nextcloud manual for installing apps in your Nextcloud instance.

### Configure samba on host

Samba should be correctly installed and configured on the host running Nextcloud snap. Both SMB and CIFS file sharing protocol should be installed on the host. Refer to your distribution documentation for information. Refer to your distribution documentation for mounting devices or shares in `fstab`.

----

### Mount directory

Removable media **must** be mounted to either `/media` or `/mnt` as **root** with **root** permissions and **connected** to Snap! 

> [!TIP]
> Ensure USB-boot is disabled in BIOS or use the `--nofail` option in `/etc/fstab` for headless boot, especially when connecting an external USB device.

> [!TIP]
> Its **not** recommended to use the "automount" directory `/media/$USER/`. Prefer creating a dedicated directory `/media/nextcloud/` or `/mnt/nextcloud/` and mounting the dedicated directory in `/etc/fstab`.

**Other shares:**

**smb/cifs** -- ensure that **smb/cifs** FUSE-Module is correctly installed on the host and refer to the distribution documentation for mounting in `/etc/fstab`.  
**sshfs** -- ensure that **sshfs** FUSE-Module is correctly installed on the host and refer to the distribution documentation for mounting in `/etc/fstab`.  
**davfs** -- ensure that **davfs** and/or davfs2 is correctly installed on the host and refer to the distribution documentation for mounting in `/etc/fstab`.  
**sftp/ftps** -- ensure that **FTP**/s and/or **SSH** is correctly installed on the host and refer to the distribution documentation for mounting in `/etc/fstab`.

### Configure external-storage app

In order to make the above devices or shares available for Nextcloud users or groups the external-device or network-share must be added in **Settings --> External Storage**. Please also refer to your nextcloud manual.

1. Enter a name in the field **Folder-name** or path to folder in Nextcloud e.g. */Documents/Foldername* or */XT-SHARE/Device*
2. For storage type select **Local**
3. For authentication select **none** or enter user or network authentication. Note: authentication is not necessary for **fstab** mounts as root (please refer to **fstab** documentation)
4. For configuration enter the path to mount point e.g. **/media/DEVICENAME** or **/media/LINKNAME**
5. User and group permissions as well as read/write permissions may be configured in the options menu in external storage app
6. Mount options may be configured in the options menu in external storage app

----

## Connect home directory

In a self-hosted environment on **Ubuntu** where **SSH** is available on the local network, yet blocked externally (firewall/router), you can access any **local non-root** directory via SSH, such as your home directory by connecting to local server via SFTP using the [external storage app](https://docs.nextcloud.com/server/25/admin_manual/configuration_files/external_storage_configuration_gui.html#enabling-external-storage-support).

<img src="https://user-images.githubusercontent.com/54933878/218519969-ff6ae69f-1f29-4f2a-b1c7-a5429cebdac1.png" width=50% height=50%>

----
