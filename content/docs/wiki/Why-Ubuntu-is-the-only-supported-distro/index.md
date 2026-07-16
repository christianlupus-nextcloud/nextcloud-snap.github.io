---
date: '2026-07-16T21:01:47+02:00'
draft: false
title: 'Why Ubuntu Is the Only Supported Distro'
---
First of all, we think the snap technology is amazing-- in fact, we helped create it. We maintain (and ourselves use) this snap because of it. But we don't care how cool technology is if it puts you, our dear users, at risk.

Canonical has done the world a disservice by spreading snapd as far and wide as they have. It's of course in their best interest to try and get snaps everywhere, but the technology underlying how snaps work either isn't present everywhere, or doesn't work the same way. As a result, despite what Canonical claims, _snaps don't run the same way everywhere_. And us snap maintainers and you users are the ones left holding the bag. Let me explain.

Some of these issues are silly little things, like [snapd making assumptions about how distros are configured](https://bugs.launchpad.net/snapd/+bug/1881189) that aren't always true, and cause snaps to break. Those are certainly annoyances, but there is an entirely different class of issue that makes this whole cross-distro story tremendously dishonest and dangerous.

Most non-Ubuntu distributions don't have all the underlying security tech that snaps require (most of the time what's missing is apparmor). For those distros, snapd transparently (without warning) runs in a degraded-security state, where confinement is completely broken and snaps can access the host system. In snaps, services run as root. Traditionally such things are terrifying, but it's okay in Ubuntu because snaps are confined, which means even though the service is running as the root user, it's a confined root without any real special privileges. However, **on non-Ubuntu distributions that lack proper confinement, snaps that have services are running as _REAL_ root, with no confinement**[1].

Beyond the horror that is the security story there, the lack of confinement causes far more mundane issues. We've seen bugs logged about MySQL that ended up being caused because the snap was running the binaries from the host system instead of the ones in the snap, when such things should be entirely off-limits for the snap. Ditto for PHP, and various configuration files. The list goes on.

What does this mean for you? If you're running snaps on non-Ubuntu distributions, be aware that in almost all cases, your snaps are at best only partially confined (e.g. seccomp filters and cgroups tend to be pretty widely available, but your kernel needs to be properly configured). Also, be aware that since snaps actually do run differently across distros, you're running the snap in a way that the maintainer almost certainly hasn't tested. The author recommends avoiding snaps entirely unless you're sure your confinement story is complete (i.e. don't run snaps on non-Ubuntu distros unless you really know what you're doing).

At this point I hope it's clear why Ubuntu is the only distro supported by the Nextcloud snap.

[1]: Snapd later introduced the concept of [system usernames](https://snapcraft.io/docs/system-usernames), but it was far too late and doesn't solve the confinement problem, and the default is still root. Also, it's unclear how well it works on other distributions.