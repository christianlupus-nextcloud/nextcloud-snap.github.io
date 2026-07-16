---
date: '2026-07-16T21:02:02+02:00'
draft: true
title: 'Wiki Change Data Directory'
---
# Change Nextcloud snap data directory

#### [Change data directory to use another disk partition](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Change-data-directory-to-use-another-disk-partition)

## **1. Create and mount data directory**

[Removable media](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#removable-media) can be any device (e.g. external-disk-partition, NFS-network mount, ZFS-device, SSHFS-network mount etc.). 
The device should be available at system boot and mounted to `/media/` or `/mnt/` via `/etc/fstab`.

> [!TIP]
> Ensure USB-boot is disabled in BIOS or use the `--nofail` option in `/etc/fstab` for headless boot (i.e. server install), especially when connecting an external USB-device.

> [!TIP]
> Prefer creating a dedicated directory in `/media/MYDIRECTORY/` or `/mnt/MYDIRECTORY/` mounting that dedicated directory in `/etc/fstab` instead of Ubuntu's "automount" `/media/$USER/DEVICE/` directory.

> [!IMPORTANT]
> Removable media **must** be mounted to either `/media` or `/mnt` as **root** with **root** permissions and **connected** to Snap! 

## **2. Connect media to snap**

#### [Connect removable media](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#removable-media)

[Removable media](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#removable-media) **must** be mounted to either `/media` or `/mnt` as **root** with **root** permissions and **connected** to Snap! 

```
sudo snap connect nextcloud:removable-media
```
**common error message** when media is not connected to Snap
```
Internal Server Error

The server encountered an internal error and was unable to complete your request.
Please contact the server administrator if this error reappears multiple times, please include the technical details below in your report.
More details can be found in the server log.
```

## **3. Edit configuration file `config.php`**

See [editing configuration file `config.php`](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#nextcloud-snap-editing-configuration-file)

### Change data directory procedure a) [**move**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Change-data-directory-to-use-another-disk-partition#if-you-have-already-created-an-admin-user) or b) [**define**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Change-data-directory-to-use-another-disk-partition#if-you-just-installed-the-snap-and-havent-created-an-admin-user-yet)

The procedure to a) [**move**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Change-data-directory-to-use-another-disk-partition#if-you-have-already-created-an-admin-user) or b) [**define**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Change-data-directory-to-use-another-disk-partition#if-you-just-installed-the-snap-and-havent-created-an-admin-user-yet) the data directory in Nextcloud snap is different when an **admin-user** exists or an **admin-user** needs to be created:

**a)** [**Move**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Change-data-directory-to-use-another-disk-partition#if-you-have-already-created-an-admin-user) data directory **after** installation (**admin-user** exists) in `/var/snap/nextcloud/current/nextcloud/config/config.php`

Example: `/var/snap/nextcloud/current/nextcloud/config/config.php`
```
 // ...
 'datadirectory' => '/media/nextcloud/data',
 // ...
 ```

**OR** 

**b)** [**Define**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Change-data-directory-to-use-another-disk-partition#if-you-just-installed-the-snap-and-havent-created-an-admin-user-yet) data directory **before** installation (**admin-user** needs to be created) in `/var/snap/nextcloud/current/nextcloud/config/autoconfig.php`

Example: `/var/snap/nextcloud/current/nextcloud/config/autoconfig.php`
 ```
  // ...
 'directory' => '/media/nextcloud/data',
 // ...
 ```
 >**Tip**: Ensure installation is completed and **admin-user** is created before rebooting

## **4. Path to data directory**

By default, the data directory in Nextcloud snap is `.../data`!
````
'datadirectory' => '/var/snap/nextcloud/common/nextcloud/data'
```` 
So regardless which procedure is used, the path to the data directory must include the complete path **including** `.../data` because this is where the required `.ocdata` or `.ncdata` file is located.

**Common error message** for incorrect path in config file
```
Error
Your data directory is invalid.

Ensure there is a file called ".ocdata" or ".ncdata" in the root of the data directory.
```

<p align="center" width="100%">
    <img width="50%" src="https://github.com/user-attachments/assets/fcfe17db-7f58-408d-a550-8bffe6df7fcd" alt=".ncdata error">
</p>

----

### Connecting a ZFS device?**

Zpools may be bind mounted to accomplish data access by the snap 

> For example, create a new directory (e.g. `mkdir -p /mnt/zpool_nc`)
>
> Depending on where your zpool exists (e.g. `/pool/tank0`), you can create a
  bind mount to allow the snap to acces/manage your data

`mount -o bind   /pool/tank0 /var/snap/nextcloud/common/nextcloud/data`

see also [path to data directory](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Wiki-Change-data-directory#path-to-data-directory)

----
> [!CAUTION]
> [**Pro-TIP!**](https://help.nextcloud.com/t/how-to-change-datadir-in-nextcloud-snap/205210)
>
> * Issue [occ-command](https://github.com/nextcloud-snap/nextcloud-snap/wiki/OCC-management-console), making sure that `<YOUR PATH>` is mounted to either `/media` or `/mnt` as **root** with **root** permissions and [**connected**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Wiki-Change-data-directory/_edit#connect-removable-media) to Snap!
> ````
> sudo nextcloud.occ maintenance:install --data-dir <YOUR PATH> --admin-user <YOUR USERNAME>
> ````
> * update [trusted domains](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-trusted-domains)
> ```
> sudo nextcloud.occ config:system:set trusted_domains 0 --value="cloud.yourdomain.com"
>```
> * disable maintenance mode 
> ```
> sudo nextcloud.occ maintenance:mode --off
> ```

