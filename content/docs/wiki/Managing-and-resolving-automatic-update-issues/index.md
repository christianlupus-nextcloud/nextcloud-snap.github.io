---
date: '2026-07-16T21:02:09+02:00'
draft: true
title: 'Managing and Resolving Automatic Update Issues'
---
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

> [!TIP]
> ### 2.1 Backup your running snap now!
>This would be the perfect opportunity to create a complete Nextcloud snap backup including configuration, apps, certificates, database and data.
>
>* stop Nextcloud snap `sudo snap stop nextcloud`
>* save Nextcloud snap `sudo snap save nextcloud`
>* start Nextcloud snap `sudo snap start nextcloud`
>* copy/move snapshot (*.zip) in `/var/lib/snapd/snapshots` to wherever
>
>see [Backup, restore & migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore)

### 3. Resolve auto-update issues

A prevalent cause of auto-update issues is attributed to [**incompatible third party apps**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#known-incompatible-third-party-apps) due to [snap confinement](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#q-what-is-snap-confinement). Any apps requiring access to executable binaries will cause auto-updates to fail since these are not included in the snap. Disabling the [misbehaving apps](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#known-incompatible-third-party-apps) usually resolves the auto-update issue. Sometimes apps can be re-installed or re-enabled after successful manual refresh (update)... but may recur during the next auto-update.

[**Known incompatible third party apps**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#known-incompatible-third-party-apps)

 If your Nextcloud interface is unavailable or in [**Maintenance mode**](https://docs.nextcloud.com/server/latest/admin_manual/maintenance/backup.html#maintenance-mode), you can disable apps using the [**management console**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/OCC-management-console) or command line:

1. List apps: ` sudo nextcloud.occ app:list` 
2. Disable app:  `sudo nextcloud.occ app:disable <appname>`
    * Remove app: `sudo nextcloud.occ app:remove <appname>` 
3. Refresh snap: `sudo snap refresh nextcloud`

----

## Resolve failed integrity check "Integrity check failed... invalid files"


>Some files have not passed the integrity check. Further information on how to resolve this issue can be found in the [documentation ](https://docs.nextcloud.com/server/27/go.php?to=admin-code-integrity):arrow_upper_right:. (List of invalid files…)


If you get a message that the integrity check has failed due to invalid files, check your [logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs) for the invalid files. There is usually a link in the integrity failure message with a list of the files causing integrity failure. Within that listing there is usually a reference to the directory from which you may deduce the app name (i.e "spreed"). 

> [!IMPORTANT]
> #### Read the [documentation](https://docs.nextcloud.com/server/27/admin_manual/issues/code_signing.html#fixing-invalid-code-integrity-messages)
> See the link to list of invalid files! ✔

### Steps to resolve integrity check failure

1. find the files causing the issue in your [logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs) or by following the link in the message and discover which app (`<appname>`) those files reference
2. double check the app name in question `sudo nextcloud.occ app:list`
3. remove the app in question `sudo nextcloud.occ app::remove <appname>`
4. reinstall the app `sudo nextcloud.occ app:enable <appname>`
5. run the integrity check manually and see output`sudo nextcloud.occ integrity:check-core`
6. run the integrity check on the app `sudo nextcloud.occ integrity:check-app <appname>`

The message should disappear. You may want to [truncate your logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs#truncate-logs).


