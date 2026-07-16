---
date: '2026-07-16T21:01:18+02:00'
draft: false
title: 'Getting Help'
---
<h1 align="center">Getting help</h1>

There are various factors that could cause an issue with your Nextcloud snap instance. Your [logs](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-logs) may help find a probable cause. There is a good chance someone in the community has had a similar issue and knows how to resolve it. That depends on how you request assistance. So read your request for tone, avoiding hyperbole and assuming good intentions.

## Requirements
Since [snaps](https://ubuntu.com/core/services/guide/snaps-intro) are Ubuntu-centric, its no surprise that Nextcloud snap was designed to run on Ubuntu and the reason why [Ubuntu is the only supported distribution](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Why-Ubuntu-is-the-only-supported-distro) for Nextcloud snap. 

## Don't panic!
> [!IMPORTANT]
> Relax, take a deep breath, get a cup of tea and consider your options. Recollect steps taken, note recent changes and structure that information. Do the basics and try to get a grasp of the situation.

If you're having a Nextcloud snap issue and you've come this far, then you're on the right track!

1. Ensure nothing was missed:
   * check the [requirements](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Installation-requirements)
   * consult our [Wiki](https://github.com/nextcloud-snap/nextcloud-snap/wiki)
   * check [configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap)
   * consult the [FAQ's](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's)
   * check for similar [issues](https://github.com/nextcloud-snap/nextcloud-snap/issues?q=is%3Aissue+is%3Aclosed)
   * check upstream [issues](https://help.nextcloud.com/tag/snap)
2. Having an issue with a [third party app](https://github.com/nextcloud-snap/nextcloud-snap/wiki/FAQ's#q-nextcloud-snap-is-not-working-because-of-third-party-app-incompatibility-what-can-i-do)? Please Log an issue against the app
3. Run the [debugging script](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Issue-Debugging-Scripts#debugging-script) before raising an issue
4. Create an [issue](https://github.com/nextcloud-snap/nextcloud-snap/issues) and complete the bug report (the more information the better)

### Disaster recovery

Depending on the type of failure you've experienced, there is a good chance of recovering your data. By default all the stuff you care about is in the `/var/snap/nextcloud` directory. So just copy the entire directory to another drive or machine. In the worst case you could lose your database, but at least you'll preserve your raw data. Your data can be found in `/var/snap/nextcloud/common/nextcloud/data` if you haven't changed your data directory. See [where is my stuff](https://github.com/nextcloud-snap/nextcloud-snap#where-is-my-stuff).

### Backup your data

Not having a backup makes a bad situation worse. There is nothing anyone can do if you don't backup your data. A good backup strategy will enable data recovery in the case of disaster. So depending on your backup frequency and how often you practice restoring your backup, you'll have a good chance of system recovery within a couple of hours with limited data loss. See [backup, restore & migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore) Nextcloud snap.