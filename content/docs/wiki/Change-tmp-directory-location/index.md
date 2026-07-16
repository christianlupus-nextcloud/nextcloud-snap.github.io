---
date: '2026-07-16T21:02:00+02:00'
draft: true
title: 'Change Tmp Directory Location'
---
# Change Nextcloud snap tmp directory location

Like it's explained [here](https://github.com/nextcloud-snap/nextcloud-snap/issues/2962#issuecomment-2703532223), if you have a use case where you are syncing a lot of small files to a few computers, you might want to move the load off your main drive, which is likely an SSD, and onto your storage drive.

Since the snap [does not allow to modify the file where this is specified](https://github.com/nextcloud-snap/nextcloud-snap/issues/2962#issuecomment-2476378796) (`/snap/nextcloud/current/config/php/php-fpm.d/www.conf`), the only way to do so is the one explained [here](https://github.com/nextcloud-snap/nextcloud-snap/issues/2962#issuecomment-3858765232):

1. Stop nextcloud:
   ```
   sudo snap stop nextcloud
   ```
2. Move the folder where nextcloud stores it's temporary files:
   ```
   sudo mv /var/snap/nextcloud/common/nextcloud/tmp /var/snap/nextcloud/common/nextcloud/_tmp
   ```
3. Create a symbolic link named like the original nextcloud tmp directory, targeting a directory from the data drive:
   ```
   sudo ln -s <directory-from-data-drive-for-tmp> /var/snap/nextcloud/common/nextcloud/tmp
   ```
4. Start nextcloud again:
   ```
   sudo snap start nextcloud
   ```