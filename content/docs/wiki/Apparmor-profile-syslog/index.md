---
date: '2026-07-16T21:01:15+02:00'
draft: true
title: 'Apparmor Profile Syslog'
---
# Reloading Apparmor profiles after updates
## Nextcloud snap fails to load due to updated apparmor profiles

https://github.com/canonical/lxd/issues/18378

Test whether apparmor is preventing Nextcloud snap from starting:

```bash
sudo aa-complain /usr/lib/snapd/snap-confine && sudo snap restart nextcloud
```
if Nextcloud snap fires up after setting the "snap-confine" profile to `complain` instead of `enforce`, then your Apparmor profiles need to be re-initialised! When apparmor profiles are updated or new profiles are added, snapd needs to be restarted in order to re-initialise them.

Restart snapd to re-initialise updated profiles by issuing the following command:

`snap disable snapd && snap enable snapd` 

re-enable apparmor profile after re-initialising apparmor profiles
```bash
sudo aa-enforce /usr/lib/snapd/snap-confine && sudo snap restart nextcloud
``` 

## Steps to resolve Apparmor update issues when running snaps on LXD/LXC as snap

* **on (LXD) host**: `snap disable snapd && snap enable snapd` to re initialise updated Apparmor profiles on host
* **on (LXC) host** running Nextcloud snap: `apt update && apt upgrade` -->  issue command: `snap disable snapd && snap enable snapd` to re initialise Apparmor profiles on LXC
* the Apparmor documentation mentions "snap is different" but doesn't explain why and how. Due to running LXD as snap on the host system (bare-metal), Apparmor profile updates/upgrades should be re initialised after reboot. LXC running on that (LXD) host will not re initialise Apparmor profile changes automatically and will need to be re initialised manually by disabling and re enabling snapd on LXC.

Since running LXD as a snap is literally running containerised LXD, with LXC-containers running a containerized Nextcloud snap, thus running containers within containers in a container, causing Apparmor profile version inconsistencies within nested containers.

Upgrading and re initialising Apparmor profiles within each nested container step by step seems the only method to consistently make Nextcloud snap available after Apparmor profile upgrades.



# Apparmor profile `syslog` warnings

In some cases **apparmor="DENIED"** `syslog` warnings on the host system are "complaining" about failing Apparmor audits. These warnings are nothing to worry about since the snap is doing exactly what it should and **confining** the snap! The warning means, that automated Apparmor audits are failing because the request is being denied due to snap confinement. Not being able to audit those profiles should have no effect on the Nextcloud snap.

```
audit: type=1400 audit(1732273383.121:15114): apparmor="DENIED" operation="ptrace" namespace="root//lxd-NEXTCLOUD_<var-snap-lxd-common-lxd>" profile="snap.nextcloud.nextcloud-cron" pid=2057758 comm="ps" requested_mask="read" denied_mask="read" peer="unconfined"
kernel
```
Apparmor comes with a predefined profiles, but there are also additional upstream Apparmor profiles which may reduce log warnings;

```
sudo apt install apparmor-profiles-extra
```

## Install `squashfuse` on LXC host
* Nextcloud snap in LXC --> recommends `sudo apt install squashfuse` in LXC host.

## Apparmor profile auditing

However if you're "allergic" to log "spamming" or believe these log entries are causing issues, let Apparmor scan the logs on the host system and **audit/correct** the profiles causing those log entries. 

> [!CAUTION]
> This procedure is not required and should only be executed if you know what you're doing and your Nextcloud snap host is secured within a container (LXD/LXC) or vm.

Apparmor has tools for auditing/correcting profiles which are not installed by default.

+ install apparmor utilities on the host `sudo apt install apparmor-utils` 
+ run `sudo aa-logprof` to scan the logs for misbehaving Apparmor profiles and correcting these

You'll be presented with a list of misbehaving Apparmor profiles which can be audited/corrected by the Apparmor-log-profiler.

Documentation:
+ https://help.ubuntu.com/community/AppArmor
+ https://ubuntu.com/server/docs/apparmor

Discussion:
+ https://github.com/nextcloud-snap/nextcloud-snap/issues/2971
