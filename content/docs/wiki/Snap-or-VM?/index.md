---
date: '2026-07-16T21:01:55+02:00'
draft: false
title: 'Snap or VM?'
---
Some people are wondering when they should prefer the snap over the VM or even Dockers which are available from other repositories.

Here is our answer:

The snap is nice for getting up and running quickly with minimal space, and will work great if you're happy not messing with it. It's a quality, optimized LAMP stack that will scale just as well as a "normal" Nextcloud installation; it's made for production. Since it's a snap you also get the benefit of automatic updates and the ability to rollback without worrying about needing to take a snapshot, etc.

However, the snap is very opinionated. Don't want to use Apache? Sorry, the snap uses it. Don't want to use MySQL? Sorry, that's what the snap uses. Something other than PHP whatever-we're-using? We picked the version we feel gives the best results. In other words, it's not very tinker-friendly. We don't do this to be mean, **we do this so that we can reliably update it without your needing to worry about it**.

The VM is much more flexible. It's a full version of Ubuntu server edition, allowing you to tweak whatever you need and it comes with many apps which are not that easy to configure for inexperienced administrators. This of course makes it larger. You'll also need to make sure you maintain it and keep the OS up-to-date. Since it's virtualized you can assign disk, CPU, memory, and network quotas to it (you'd need to install the snap in an lxc container or a VM to get the same abilities).

Adapted from https://github.com/nextcloud/nextcloud-snap/issues/236