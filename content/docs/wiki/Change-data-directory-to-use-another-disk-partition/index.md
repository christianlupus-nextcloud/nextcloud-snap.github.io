---
date: '2026-07-16T21:01:42+02:00'
draft: false
title: 'Change Data Directory to Use Another Disk Partition'
---
# Prerequisites

The partition you want to use must be mounted _somewhere_ in `/media/` or `/mnt/`. These are the only locations the snap can access under confinement with the `removable-media` plug. Connect the `removable-media` plug as mentioned in the [README](https://github.com/nextcloud/nextcloud-snap/blob/master/README.md) in order to grant the snap permission to access external drives.

# If you just installed the snap, and haven't created an admin user yet

1. Decide where you want the new data to live. We'll use `/media/nextcloud/data`. Make sure `root` has write access, but it's not accessible by others, for example:

        $ sudo mkdir -p /media/nextcloud/data
        $ sudo chown -R root:root /media/nextcloud/data
        $ sudo chmod 0770 /media/nextcloud/data

    **Important:** Posix permissions take the _entire_ path into account. Even if the `data` directory above is writable by root, if any part of the path above it (e.g. `/media`, or `/media/nextcloud`) is not accessible by root, it will not be able to access `data` either.

2. Update the Nextcloud config to use the new data directory by editing `/var/snap/nextcloud/current/nextcloud/config/autoconfig.php` and making sure the `directory` setting is pointing to the right place, e.g.:

        // ...
        'directory' => '/media/nextcloud/data',
        // ...

3. Now, restart the PHP service:

        $ sudo snap restart nextcloud.php-fpm

   If you visited Nextcloud before now, refresh to re-evaluate the changed config (otherwise the `directory` won't change from the default).

4. Make your admin user as usual and carry on, Nextcloud is using the external data.


# If you have already created an admin user

1. Decide where you want the new data to live. We'll use `/media/nextcloud/data`, but the `data` directory will be created by moving the existing data into place, so we only need to setup `/media/nextcloud` right now (although you'll still want to make sure it's owned by root):

        $ sudo mkdir -p /media/nextcloud
        $ sudo chown -R root:root /media/nextcloud

    **Important:** Posix permissions take the _entire_ path into account. Even if the `data` directory above is writable by root, if any part of the path above it (e.g. `/media`, or `/media/nextcloud`) is not accessible by root, it will not be able to access `data` either.

2. Stop the snap from running for a moment:

        $ sudo snap stop nextcloud

2. Update the Nextcloud config to use the new data directory by editing `/var/snap/nextcloud/current/nextcloud/config/config.php` and making sure the `datadirectory` setting is pointing to the right place, e.g.:

        // ...
        'datadirectory' => '/media/nextcloud/data',
        // ...

4. Move (or copy) the current data directory to the new place:

        $ sudo mv /var/snap/nextcloud/common/nextcloud/data /media/nextcloud/

5. Restart the snap:

        $ sudo snap start nextcloud

And you should be up and running using external storage for Nextcloud's data.