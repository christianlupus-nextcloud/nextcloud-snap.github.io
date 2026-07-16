---
date: '2026-07-16T21:01:54+02:00'
draft: true
title: 'FAQ''s'
---
<h1 align="center">Nextcloud snap FAQ's</h1>

## **Q: Why is Nextcloud snap sometimes out of sync with upstream Nextcloud?**

Nextcloud snap is designed to be easy to install and simple to maintain. The ideal Nextcloud snap is an "install and forget" Nextcloud instance that works on most architectures and updates itself without needing administrative skills. Combining Nextcloud with snapd makes it a perfect fit for IoT or scalable environments. Snapd is a secure and robust technology which the Nextcloud snap team has embraced.  

The Nextcloud snap team packages a stable default upstream Nextcloud, adds some snap magic, tests and releases that snap package. Since the Nextcloud snap team are neither upstream Nextcloud developers nor app developers, they need to ensure that the default Nextcloud snap does what it should. That entails packaging a stable default Nextcloud into a neat, easy to install package and testing fresh installs as well as automated updates, so that the Nextcloud snap community has peace of mind.

Sometimes the Nextcloud snap team needs to wait for some Nextcloud issues to be resolved upstream before releasing a stable default Nextcloud snap to the public. Some upstream changes may need more snap magic to ensure that it simply works, which requires careful configuration and testing. This **excludes** non default third party apps! 

----
## **Q: Why should I prefer snap over VM or Docker?**

> [!NOTE]
>
> Some people are wondering when they should prefer the snap over the VM or even Dockers which are available from other repositories.
>
> The snap is nice for getting up and running quickly with minimal space, and will work great if you're happy not messing with it. It's a quality, optimized LAMP stack that will scale just as well as a "normal" Nextcloud installation; it's made for production. Since it's a snap you also get the benefit of automatic updates and the ability to rollback without worrying about needing to take a snapshot, etc.
>
> However, the snap is very opinionated. Don't want to use Apache? Sorry, the snap uses it. Don't want to use MySQL? Sorry, that's what the snap uses. Something other than PHP whatever-we're-using? We picked the version we feel gives the best results. In other words, it's not very tinker-friendly. We don't do this to be mean, we do this so that we can reliably update it without your needing to worry about it.
>
> The VM is much more flexible. It's a full version of Ubuntu server edition, allowing you to tweak whatever you need and it comes with many apps which are not that easy to configure for inexperienced administrators. This of course makes it larger. You'll also need to make sure you maintain it and keep the OS up-to-date. Since it's virtualized you can assign disk, CPU, memory, and network quotas to it (you'd need to install the snap in an lxc container or a VM to get the same abilities).

----

## **Q: How can I manage automatic updates?**

Nextcloud snap is designed to update (refresh) automatically by default. 

[**You have full control**](https://snapcraft.io/docs/managing-updates) to manage [**if** and **when**](https://snapcraft.io/docs/managing-updates) your Nextcloud snap is updated with snapd daemon! See [managing updates](https://snapcraft.io/docs/managing-updates).

> [!NOTE]
> Snaps update automatically, and by default, the snapd daemon checks for updates 4 times a day. Each update check is called a refresh.
> Updates can be set to occur on Friday at midnight, or for specific days of the month, such as only the third Monday, or even the last Friday of the month, between 23:00 to 01:00 the next day. They can even be postponed indefinitely, or for a set period of time.
> When, if, and how often, these updates occur is the job of the snap refresh command.

----

## **Q: Nextcloud snap stopped working after an automatic update. What can I do?**

First of all, let's get you back up and running. Snaps were designed to have a robust upgrade story. In the case of the Nextcloud snap, every time it refreshes (updates), it takes a snapshot of the database and other version-specific things (not the raw data though, e.g. pictures and documents) and holds them alongside the old revision of the snap.

```bash
sudo snap revert nextcloud
```

With that command, you should be back up and running right at the moment before the update happened.


* [what to do if an automatic update breaks my Nextcloud](https://github.com/nextcloud-snap/nextcloud-snap/wiki/What-do-I-do-if-an-update-breaks-Nextcloud%3F)
* [how to manage automatic updates with snapd](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-automatic-updates)
* [how to revert Nextcloud snap after a failed automatic update](https://help.nextcloud.com/t/how-to-revert-nextcloud-snap-after-failed-update/216034)
* [how to resolve automatic update issues](https://help.nextcloud.com/t/how-to-manage-failed-automatic-update-in-nextcloud-snap/230652)

----

## **Q: Nextcloud snap shows security & setup warnings. What can I do?**

### "Imagick PHP module is not enabled"

<p align="center" width="100%">
    <img width="50%" src="https://github.com/nextcloud-snap/nextcloud-snap/assets/54933878/1ac6050f-4371-4a87-ba5c-04ce5e8c7917" alt="imagick warning">
</p>

This warning is **only** a suggestion. The "theming app" uses Imagick to generate favicons. The theming app works fine without the Imagick PHP module enabled, but this may differ from system to system. 

> [!NOTE]
> You will have to live with that warning... Nextcloud snap does not ship the Imagick PHP module!

> [!CAUTION]
> Imagick is a security concern, and as such **isn't enabled on purpose**! You see this warning because the theming app uses Imagick to generate favicons. As a result, that particular feature (favicon generation) isn't possible while using the snap.

----
### "No High-performance backend configured"

<p align="center" width="100%">
    <img width="50%" src="https://github.com/user-attachments/assets/110dbaee-a276-4175-8986-7a78828f91a6" alt="HPB warning">
</p>

disable setup warning in **Administration settings** --> **Talk** --> _disable warning_

<p align="center" width="100%">
    <img width="50%" src="https://github.com/user-attachments/assets/f6a7ef55-4223-4a37-8f24-7b1084ac0dac" alt="disable HPB warning">
</p>

----
## **Q: Add missing indices warning is displayed, how can I add missing indices to the database?**

![grafik](https://github.com/nextcloud-snap/nextcloud-snap/assets/54933878/1fcd24eb-68c5-434f-a773-2da1b9e4bd47)

The Nextcloud snap "fixer" service `nextcloud-fixer` handles these missing indices for you. Be patient this may take a couple of minutes depending on your architecture. 

If you're impatient you can add missing indices manually:

```bash
sudo nextcloud.occ db:add-missing-indices
```

----
## **Q: "One or more mimetype migrations are available..." warning is displayed, what can I do?**

![grafik](https://github.com/user-attachments/assets/91ac3188-7409-4bc6-9f10-569e23ac3e3f)

After automatic upgrade the following upstream warning may be displayed `One or more mimetype migrations are available. Occasionally new mimetypes are added to better handle certain file types. Migrating the mimetypes take a long time on larger instances so this is not done automatically during upgrades.` which can be resolved by issuing command:

```bash
sudo nextcloud.occ maintenance:repair --include-expensive
```

> [!IMPORTANT]
> The following mimetype migrations may take a while depending on system resources, so be patient!

----
## **Q: "Integrity check failed... invalid files" what can I do?**


>Some files have not passed the integrity check. Further information on how to resolve this issue can be found in the [documentation ](https://docs.nextcloud.com/server/27/go.php?to=admin-code-integrity):arrow_upper_right:. (List of invalid files…)


If you get a message that the integrity check has failed due to invalid files, check your [logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs) for the invalid files. There is usually a link in the integrity failure message with a list of the files causing integrity failure. Within that listing there is usually a reference to the directory from which you may deduce the app name (i.e "spreed"). 

> [!IMPORTANT]
> #### Read the [documentation](https://docs.nextcloud.com/server/27/admin_manual/issues/code_signing.html#fixing-invalid-code-integrity-messages)

### Steps to resolve integrity check failure

1. find the files causing the issue in your [logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs) or by following the link in the message and discover which app (`<appname>`) those files reference
2. double check the app name in question `sudo nextcloud.occ app:list`
3. remove the app in question `sudo nextcloud.occ app::remove <appname>`
4. reinstall the app `sudo nextcloud.occ app:enable <appname>`
5. run the integrity check manually and see output`sudo nextcloud.occ integrity:check-core`
6. run the integrity check on the app `sudo nextcloud.occ integrity:check-app <appname>`

The message should disappear. You may want to [truncate your logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs#truncate-logs).

----
## **Q: Incorrect version displayed on [Nextcloud security scan](https://scan.nextcloud.com/)**

If you have previously scanned your domain or subdomain via that scan page, it shows **cached data from previous scan** by default. There is a button called **trigger re-scan** → Try hitting that button, then wait for 5 minutes to revisit that scan page to query your domain or subdomain.

<p align="center" width="100%">
    <img width="40%" src="https://github.com/user-attachments/assets/8155f926-bb33-48f1-afda-8410edc96b95" alt="security scan">
</p>

----
## **Q: My Nextcloud snap stopped working, I have no Backup, where is my data?**

There are various factors that could cause an issue with your Nextcloud snap instance. Your [logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs) may help find a probable cause. There is a good chance someone in the community has had a similar issue and knows how to resolve it. That depends on how you request assistance. So read your request for tone, avoiding hyperbole and assuming good intentions.

> [!IMPORTANT]
> Most importantly **don't panic!** Relax, take a deep breath, get a cup of tea and consider your options. Recollect all steps taken and note recent changes and then structure that information. Do the basics and try to get a grasp of the situation. 

See [**getting help**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Getting-help)

> [!CAUTION]
> Not having a backup makes a bad situation worse. There is nothing anyone can do if you don't backup your data. A good backup strategy will enable data recovery in the case of disaster. So depending on your backup frequency and how often you practice restoring your backup, you'll have a good chance of system recovery within a couple of hours with limited data loss. See [backup, restore & migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore) Nextcloud snap.

> [!TIP]
> Depending on the type of failure, there is a good chance of recovering your raw data. By default all the stuff you care about is in the `/var/snap/nextcloud` directory. So just copy the entire directory to another drive (or machine). In the worst case you lose your database, but at least you'll preserve your raw data. Your raw data can be found in `/var/snap/nextcloud/common/nextcloud/data` if you haven't changed your data directory.

See [what to do if an update breaks my Nextcloud](https://github.com/nextcloud-snap/nextcloud-snap/wiki/What-do-I-do-if-an-update-breaks-Nextcloud%3F)

----
## **Q: Nextcloud snap is not working because of **third party app** incompatibility. What can I do?**

> [!IMPORTANT]
> Some **third party apps** are incompatible with Nextcloud snap. We do not maintain these apps and thus we can't fix anything within them to make them work, nor do we have the manpower to do so. If you're having issues with a **third party app**, please log an issue against the app itself.

First check your [logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs) to discover which **third party app** is causing an issue. Then disable the app in the Nextcloud administration apps interface. If your Nextcloud interface is not available or your Nextcloud is in "Maintenance mode", you can disable apps using the [management console](https://github.com/nextcloud-snap/nextcloud-snap/wiki/OCC-management-console) or command line:

  * list apps: 
```bash
   sudo nextcloud.occ app:list
```
  * disable apps: 
```bash
   sudo nextcloud.occ app:disable appname
```
  * remove apps:
```bash   
   sudo nextcloud.occ app:remove appname
```

----
## **Q: How can I enable birthday calendar for contacts?**

enable birthday calendar for contacts:

```
sudo nextcloud.occ dav:sync-birthday-calendar
```
----
## **Q: How can I improve upload performance?**

See [Improve upload performance](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Improve-upload-performance#nextcloud-upload-chunk-size)
 
----
## **Q: Nextcloud office is not working in Nextcloud snap. What can I do?**

See [Configure CODE & Nextcloud office](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-CODE-and-Nextcloud-office-for-Nextcloud-snap)

or set up an alternative: 

* [how to install and configure Collabora CODE server for Nextcloud snap with docker](https://github.com/nextcloud-snap/nextcloud-snap/wiki/How-to-configure-collabora-CODE-with-docker) 
* [how to install and configure Collabora CODE on a separate server for Nextcloud snap](https://github.com/yannicklescure/wiki-md/blob/main/nextcloud/install-configure-collabora-online-nextcloud-snap-server.md)

----
## **Q: Lets Encrypt failed after entering incorrect data. What can I do?**

So you've tried to encrypt your Nextcloud snap with [Lets Encrypt](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-HTTP-encryption-(HTTPS)#lets-encrypt) and something went wrong after entering incorrect data, you'll need to correct that data. 

You may run into a `... permission error` because only root may overwrite the data. 

Simply switch user to root `sudo -i` and try entering the correct data as root `# nextcloud.enable-https lets-encrypt`. 

If that doesn't work, you'll need to [encrypt from scratch](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-HTTP-encryption-(HTTPS)#change-lets-encrypt-domain----lets-encrypt-from-scratch). 

----
## **Q: Nextcloud snap is in **maintenance mode**. What can I do?**

Disable maintenance mode:
```bash
sudo nextcloud.occ maintenance:mode --off
```
----
## **Q: Nextcloud snap `cron` fails after update. What can I do?**

See [Managing cron](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-cron)

restart cron:
```bash
sudo snap restart nextcloud.nextcloud-cron
```

enable cron:
```bash
sudo nextcloud.occ background:cron
```
----

## **Q: I'm experiencing periodically high CPU load, what can I do?**

these regular peaks (every 15 minutes) could be caused by failing Nextcloud cron.

see [managing cron](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-cron) especially [cron fails after upgrade](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-cron#if-cron-fails-after-update)

restart cron:
```bash
sudo snap restart nextcloud.nextcloud-cron
```

enable cron:
```bash
sudo nextcloud.occ background:cron
```

----
## **Q: What permissions should external media have?**

See [Snap confinement](https://snapcraft.io/docs/snap-confinement)

[Removable media](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media) **must** be mounted to either `/media` or `/mnt` as **root** with **root** permissions and **connected** to Snap!

The interface providing the ability to access removable media is not automatically connected upon install, so if you'd like to use external storage (or otherwise use a device in /media or /mnt for data), you need to give the snap permission to access removable media by connecting that interface:
```bash
sudo snap connect nextcloud:removable-media
```
----

## **Q: How can I connect local or external media?**

See [Connect external media](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media)

```bash
sudo snap connect nextcloud:removable-media
```
----

## **Q: How can I connect a USB media device?**

See [Connect to USB device](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap#removable-media)

```bash
sudo snap connect nextcloud:removable-media
```
----

## **Q: How can I change my data directory**

see [change data directory](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Wiki-Change-data-directory)

----

## **Q: How can I set default phone region in Nextcloud snap?**

See [Setting default phone region](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-default-phone-region)

Find your [country code](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/config_sample_php_parameters.html#user-experience) using [ISO 3166-1 country codes](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes) and edit `config.php` and add/edit line in `config.php`
```
'default_phone_region' => 'GB',
```
or issue command on host replacing "GB" with your country code:

```bash
sudo nextcloud.occ config:system:set default_phone_region --value="GB"
```
----
## **Q: How can I set default maintenance window in Nextcloud snap?**

See [Setting default maintenance window](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-default-maintenance-window)

Set default maintenance window time 01:00 (1 a.m.) UTC

edit `config.php` and add/edit line in `config.php`
```text
'maintenance_window_start' => 1,
```
or issue command on host:

```bash
sudo nextcloud.occ config:system:set maintenance_window_start --value="1"
```
----

## **Q: My host system logs are showing recurring Apparmor profile audit warnings, what does it mean?**

In some cases **apparmor="DENIED"** `syslog` warnings are "complaining" about failing Apparmor profile audits. These warnings are nothing to worry about since the snap is doing exactly what it should and **confining** the snap! The warning means, that automated Apparmor audits are failing because the request is being denied due to snap confinement. Not being able to audit those profiles should have no effect on the Nextcloud snap.

```
audit: type=1400 audit(1732273383.121:15114): apparmor="DENIED" operation="example" namespace="root//lxd-NEXTCLOUD_<var-snap-lxd-common-lxd>" profile="snap.nextcloud.nextcloud-cron" pid=2057758 comm="ps" requested_mask="read" denied_mask="read" peer="unconfined"
kernel
```
Apparmor comes with a predefined profiles, but there are also additional upstream Apparmor profiles which may reduce log warnings;

```
sudo apt install apparmor-profiles-extra
```
see [Apparmor profile auditing](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Apparmor-profile-syslog#apparmor-profile-auditing)

----

## **Q: What is Snap confinement?**

[Snap confinement](https://snapcraft.io/docs/snap-confinement) is a **snap security feature on Ubuntu**. This is the reason [Nextcloud snap is not supported on non-**Ubuntu** distributions](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Why-Ubuntu-is-the-only-supported-distro). So unless you allow the Nextcloud snap to access [removable media](https://github.com/nextcloud-snap/nextcloud-snap#removable-media) in `/mnt` or `/media` you will not be able to access any other directory outside of the confinement on Ubuntu.

----

## **Q: How can I connect my home directory in Nextcloud snap?**

In a self-hosted environment on **Ubuntu** where **SSH** is available on the local network, yet blocked externally (firewall/router), you can access any **local non-root** directory, such as your home directory by connecting to local server via SFTP using the [external storage app](https://docs.nextcloud.com/server/25/admin_manual/configuration_files/external_storage_configuration_gui.html#enabling-external-storage-support).

<img src="https://user-images.githubusercontent.com/54933878/218519969-ff6ae69f-1f29-4f2a-b1c7-a5429cebdac1.png" width=50% height=50%>

## **Q: How can I set or disable the "set server ID" setup warning?** 

From Nextcloud vs. 33.0.x it is possible to share PHP services for multiple instances on bare-metal and AIO installs. 

As sharing services is not possible with Nextcloud snap,  that setup warning can be disabled for Nextcloud snap setup warnings by setting an integer value `0` in the configuration file or by issuing the command:

```
sudo nextcloud.occ config:system:set serverid --type integer --value=0
```
----
