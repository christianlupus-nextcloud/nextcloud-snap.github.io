---
date: '2026-07-16T21:01:12+02:00'
draft: false
title: 'Managing Automatic Updates'
---
<h1 align="center">Managing automatic Nextcloud snap updates</h1>

+ [Snapcraft documentation](https://snapcraft.io/docs/managing-updates)

Nextcloud snap is designed to update (refresh) automatically by default. 

[**You have full control**](https://snapcraft.io/docs/managing-updates) to manage [**if** and **when**](https://snapcraft.io/docs/managing-updates) your Nextcloud snap is updated with snapd daemon! See [managing updates](https://snapcraft.io/docs/managing-updates).

> [!NOTE]
> Snaps update automatically, and by default, the snapd daemon checks for updates 4 times a day. Each update check is called a refresh.
> Updates can be set to occur on Friday at midnight, or for specific days of the month, such as only the third Monday, or even the last Friday of the month, between 23:00 to 01:00 the next day. They can even be postponed indefinitely, or for a set period of time.
> When, if, and how often, these updates occur is the job of the snap refresh command.

## Resolve failed automatic update issues in Nextcloud snap

### 1. Check your logs to discover the cause of the update failure
* See [managing logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs)
* Run the [debugging script](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Issue-Debugging-Scripts#debugging-script)

### 2. Revert to last known good revision

Snaps were designed to have a robust upgrade story. In the case of the Nextcloud snap, every time it refreshes (updates), it takes a snapshot of the database and other version-specific things (not the raw data though, e.g. pictures and documents) and holds them alongside the old revision of the snap.

```
sudo snap revert nextcloud
```
With that command, you should be back up and running right at the moment before the update happened.

see [FAQ's](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#q-nextcloud-snap-stopped-working-after-an-automatic-update-what-can-i-do)

#### 2.1 Backup your running snap now!
This would be the perfect opportunity to create a complete Nextcloud snap backup including configuration, apps, certificates, database and data.

* stop Nextcloud snap `sudo snap stop nextcloud`
* save Nextcloud snap `sudo snap save nextcloud`
* start Nextcloud snap `sudo snap start nextcloud`
* copy/move snapshot (*.zip) in `/var/lib/snapd/snapshots` to wherever

see [Backup, restore & migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore)

### 3. Resolve auto-update issues

A prevalent cause of auto-update issues is attributed to [**incompatible third party apps**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#known-incompatible-third-party-apps) due to [snap confinement](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#q-what-is-snap-confinement). Any apps requiring access to executable binaries will cause auto-updates to fail since these are not included in the snap. Disabling the [misbehaving apps](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#known-incompatible-third-party-apps) usually resolves the auto-update issue. Sometimes apps can be re-installed or re-enabled after successful manual refresh (update)... but may recur during the next auto-update.

 If your Nextcloud interface is unavailable or in [**Maintenance mode**](https://docs.nextcloud.com/server/latest/admin_manual/maintenance/backup.html#maintenance-mode), you can disable apps using the [**management console**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/OCC-management-console) or command line:

1. List apps: ` sudo nextcloud.occ app:list` 
2. Disable app:  `sudo nextcloud.occ app:disable <appname>`
    * Remove app: `sudo nextcloud.occ app:remove <appname>` 
3. Enable app: `sudo nextcloud.occ app:enable <appname>`

### 4. Manually update = refresh Nextcloud snap
```
sudo snap refresh nextcloud
```
