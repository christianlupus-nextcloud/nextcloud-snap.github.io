---
date: '2026-07-16T21:01:20+02:00'
draft: false
title: 'Backup and Restore'
---
# Snap snapshot backup & restore

Snap snapshot is a snap utility to create and manage snap snapshots. It may be used for random snapshots or automated as scripted cronjob and has the added convenience of easy snap migration. 

[Snap snapshots official documentation](https://snapcraft.io/docs/snapshots) \
[Example rotating backup script](https://github.com/scubamuc/wiki-md/blob/scubamuc-wiki/NEXTCLOUD.snap--backup_snap-snapshot.md) by [@scubamuc](https://github.com/scubamuc) \
[Example backup script](https://github.com/sbe-arg/snap-backups) by [@sbe-arg](https://github.com/sbe-arg)

## Backup Nextcloud snap with snap snapshot

Snap snapshot will backup the entire Nextcloud snap including configuration, apps, certificates, database and data into a compressed file (*.zip) located in `/var/lib/snapd/snapshots`. See what's [inside a snapshot](https://snapcraft.io/docs/snapshots#heading--anatomy).

See documentation in `man snap` and [snap snapshot documentation](https://snapcraft.io/docs/snapshots#heading--generating)

### Procedure

* stop Nextcloud snap  `sudo snap stop nextcloud`
* save Nextcloud snap  `sudo snap save nextcloud`
* start Nextcloud snap `sudo snap start nextcloud`
* copy/move snapshot (*.zip) in `/var/lib/snapd/snapshots` to wherever

## Restore or migrate Nextcloud snap with snap restore

> [!IMPORTANT]
> * When migrating be sure that Nextcloud snap is installed on the target device.
> * Snap restore restores the entire Nextcloud snap including configuration, apps, certificates, database and data.
> * **CAUTION!**: snap restore is not incremental and will completely replace existing installation.

See documentation in `man snap` and [snap snapshot documentation](https://snapcraft.io/docs/snapshots#heading--restoring)

### Procedure

* copy/move snapshot (\*.zip) to `/var/lib/snapd/snapshots`
* discover snapshot-id using `snap saved`
* restore snapshot `sudo snap restore <snapshot-id>` 

## Deleting snap snapshots

See documentation in `man snap` and [snap snapshot documentation](https://snapcraft.io/docs/snapshots#heading--deleting)

### Procedure

* discover snapshot-id using `snap saved`
* delete snapshot `sudo snap forget <snapshot-id>` 
