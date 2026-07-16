---
date: '2026-07-16T21:01:50+02:00'
draft: false
title: 'Managing External Media, Shares and Storage'
---
<h1 align="center">Managing external media, shares and storage</h1>


[Snap confinement](https://snapcraft.io/docs/snap-confinement) is a **security feature** and determines the amount of access an application has to system resources, such as files, the network, peripherals and services. Thus your Nextcloud snap is securely confined from the host system. Unless you specifically allow the Nextcloud snap to access the `/media` or `/mnt` directories on the **host** system, you will not be able to access any other directory outside of the confinement.

## External media, network shares and storage

See [Snapcraft removable media interface](https://snapcraft.io/docs/removable-media-interface)

External media can be any device (e.g. external disk partition, SAMBA network, NFS network, SSHFS network etc.). The device should be available at system boot and mounted in `/etc/fstab` on host.

## What permissions should external media have?

See [FAQ's](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's) and [Snap confinement](https://snapcraft.io/docs/snap-confinement)

[**Removable media**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media) or [**external storage**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media) **must** be mounted to either `/media` or `/mnt`  as **root** with **root** permissions and **connected** to Snap!

The interface providing the ability to access removable media is not automatically connected upon install, so if you'd like to use external storage (or otherwise use a device in `/media` or `/mnt`  for data), you need to give the snap permission to access removable media by connecting that interface:
```bash
sudo snap connect nextcloud:removable-media
```
----

## Connect local or external media

See [Connect external media](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media)

```bash
sudo snap connect nextcloud:removable-media
```
----

## Connect a USB media device

See [Connect USB device](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media)

```bash
sudo snap connect nextcloud:removable-media
```
----

## Connect a network share

See [Connect network shares](https://github.com/nextcloud-snap/nextcloud-snap/wiki/How-to-add-samba-shares-and-local-shares)

```bash
sudo snap connect nextcloud:removable-media
```
----

### Mount directory
> [!TIP]
> Ensure USB-boot is disabled in BIOS or use the `--nofail` option in `/etc/fstab` for headless boot, especially when connecting an external USB-device.

> [!TIP]
> Its **not** recommended to use the "automount" directory `/media/$USER/`. Prefer creating a dedicated directory `/media/nextcloud/` or `/mnt/nextcloud/` and mounting the dedicated directory in `/etc/fstab`.

----

## Connect home directory

In a self-hosted environment on **Ubuntu** where **SSH** is available on the local network, yet blocked externally (firewall/router), you can access any **local non-root** directory, such as your home directory by connecting to local server via SFTP using the [external storage app](https://docs.nextcloud.com/server/25/admin_manual/configuration_files/external_storage_configuration_gui.html#enabling-external-storage-support).

<p align="center" width="100%">
    <img width="50%" src="https://user-images.githubusercontent.com/54933878/218519969-ff6ae69f-1f29-4f2a-b1c7-a5429cebdac1.png" alt="first start">
</p>

## Data directory
* [Change data directory](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media)
* [Change data directory, procedure to move or define data directory](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Wiki-Change-data-directory#change-nextcloud-snap-data-directory)
* [Default path](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Default-path#where-is-my-stuff)

## Network and local shares
* [How to add samba shares and local shares](https://github.com/nextcloud-snap/nextcloud-snap/wiki/How-to-add-samba-shares-and-local-shares)

## [**Permissions and confinement**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Default-path#permissions-and-confinement)

### [Snapcraft documentation](https://snapcraft.io/docs/removable-media-interface)