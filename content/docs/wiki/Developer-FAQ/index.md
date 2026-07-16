---
date: '2026-07-16T21:01:36+02:00'
draft: true
title: 'Developer FAQ'
---
**Q: Why aren't you using MariaDB?**

A: MariaDB was historically unsupported on ARM, and at this point migrating all users from MySQL to MariaDB seems to be more work than it's worth.

----

**Q: How do I build this snap for my insanely slow ARM device?**

A: You can build on-device if you have enough memory (e.g. stick a USB drive in and create a swap file if necessary), but we recommend using the [Launchpad Snap Builders](https://kyrofa.com/posts/building-your-snap-on-device-there-s-a-better-way) to build for all architectures at once.

----

**Q: Why don't you use the distro packages for Apache?**

A: Because it's far too complicated (and includes too much) for the tiny embedded web server we need in the snap. Not to mention it does a lot of work in the post-install hooks which aren't run when building the snap. More information is available in [this blog post](https://kyrofa.com/posts/snapping-nextcloud-the-web-server).

----

**Q: Why don't you use the distro packages for PHP?**

A: Mostly because we didn't use the distro packages for Apache. More information is available in [this blog post](https://kyrofa.com/posts/snapping-nextcloud-php-7).

----