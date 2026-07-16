---
date: '2026-07-16T21:01:35+02:00'
draft: true
title: 'Export and Import'
---

Nextcloud snap includes a CLI utility for exporting and importing data, apps, configuration and database. Issuing the command `sudo nextcloud.export` will backup the entire instance into a timestamped folder.

> Note: SSL Certificates are not included in the backup!
>
> Note: `nextcloud.export` will **ignore** data in **custom** [data directories](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Wiki-Change-data-directory)!

This works fine as a cronjob (as root) and for random backups. Compressed backup may be moved wherever.

Alternative backup method: [Backup, restore & migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore)

## Procedure

### Export Nextcloud snap with `nextcloud.export`

#### View export options
```
sudo nextcloud.export -h
```
```
Usage:
    nextcloud.export [OPTIONS]
    Export data suitable for migrating servers. By default this
    includes the Nextcloud database, configuration, and data
    (equivalent to running nextcloud.export -abcd).

Available options:
    -h: Display this help message
    -a: Include the (non-default) apps
    -b: Include the database
    -c: Include the config
    -d: Include the data (can be quite large)
```

1\. Issue command: `sudo nextcloud.export -[options]`. 
Export will backup the entire instance into a timestamped folder in the backup directory `/var/snap/nextcloud/common/backups/`

2\. Move, copy or archive the timestamped folder in the backup directory: `/var/snap/nextcloud/common/backups/`

> After the export is complete the Snap will forget about it, so feel free to do whatever you like with it; one option could be to `tar` it, with a command like this:
`sudo tar -cvf 202x0x0x-235959_nextcloud-backup.tar /var/snap/nextcloud/common/backups/202x0x0x-235959`**

> This works as an automatic cronjob (as root) or for random backups. The backup may be moved/copied from `/var/snap/nextcloud/common/backups` to wherever. Maintenance mode will be automatically turned on and off during the backup process. Downtime will depend on your system resources and data volume.

### Import Nextcloud snap with `nextcloud.import`

#### View import options
```
sudo nextcloud.import -h
```
```
Usage:
    nextcloud.import [OPTIONS] <backup dir>
    Import data exported from another Nextcloud snap instance.
    By default this imports the database, config, and data
    (equivalent to running nextcloud.import -abcd).

Available options:
    -h: Display this help message
    -a: Include the (non-default) apps
    -b: Include the database
    -c: Include the config
    -d: Include the data (can be quite large)
```
### Procedure

* When moving to new device, be sure to [install Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap/wiki/install-Nextcloud-snap) first 
* `nextcloud.import` replaces previous installation including apps, config, database and data depending on export/import options

1\. Copy/move export to restore directory: `/var/snap/nextcloud/common/` or `/var/snap/nextcloud/current/` somewhere. **Make sure the path is within Snap [confinement](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#snap-confinement-in-nextcloud-snap):**

2\. Issue command including path to restore directory: `sudo nextcloud.import /var/snap/nextcloud/common/202x0x0x-235959`

> The path needs to be available from within [confinement](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#snap-confinement-in-nextcloud-snap), so copy/move the export into `/var/snap/nextcloud/common/` or `/var/snap/nextcloud/current/` somewhere. User **root** needs to be the owner.

> Renew HTTPS encryption, renew SSL Certificates. See [managing encryption](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-HTTP-encryption-(HTTPS)) to renew SSL certificate.

----

Alternative backup method: [Backup, restore & migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore)


Script Example: [rotating Nextcloud snap export script](https://github.com/scubamuc/wiki-md/blob/scubamuc-wiki/NEXTCLOUD.snap--backup_nextcloud-export.md)

