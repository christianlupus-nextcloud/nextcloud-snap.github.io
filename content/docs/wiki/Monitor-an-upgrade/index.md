---
date: '2026-07-16T21:01:49+02:00'
draft: true
title: 'Monitor an Upgrade'
---
_The following is adapted from [kyrofa's blog](https://kyrofa.com/posts/monitoring-a-nextcloud-snap-upgrade/)_

The Nextcloud snap updates automatically. By default it might happen any time, although [you can configure that](https://snapcraft.io/docs/keeping-snaps-up-to-date). The snap provides a wonderful, user-friendly indicator that an upgrade is happening: it will become unresponsive and unvisitable until the upgrade has finished. Okay, I'm kidding, this is terrible and [something we know we need to work on](https://github.com/nextcloud-snap/nextcloud-snap/issues/2016). My point is, there are times when your snap isn't responding properly, and you want to know WHY. It's probably because an upgrade is happening, so how do you monitor it?

Logs.

The Nextcloud snap has several services contained within it. There are four that are most important for upgrade purposes:

1. MySQL (`nextcloud.mysql`)
2. PHP (`nextcloud.php-fpm`)
3. Apache (`nextcloud.apache`)
4. Nextcloud Fixer (`nextcloud.nextcloud-fixer`)

These run in order and are dependent on each other, so it's informative to check them in order. Let's talk through them.

## MySQL

MySQL will run its own upgrade process when the snap update includes a new version of it. This alone can take surprisingly long (several minutes on my own production install of the snap).

In most snap updates, though, a new MySQL version is not shipped. In that case, it should fire up pretty quickly. Either way, you can monitor it with:

    $ snap logs -f nextcloud.mysql

That will open the log and follow it, so you can see what's happening in real time. To stop following it, ctrl+c out of it.

If for whatever reason that log isn't helpful to you, you can take a look at MySQL's error log, located at `var/snap/nextcloud/current/logs/mysql_errors.log`.

If MySQL looks healthy, move on to PHP.

## PHP

PHP doesn't really have an upgrade process of its own, even when the snap includes a PHP update. Which means it fires up fast and rarely has issues. That said, it's required by almost everything else, so it's important to know about it and know how to check it. You can monitor it with:

    $ snap logs -f nextcloud.php-fpm

Its error log is located at `/var/snap/nextcloud/current/logs/php-fpm_errors.log`. Assuming everything looks good, time to check Apache.

## Apache

Apache is another component that doesn't have its own upgrade process and fires up fast. However, its the service that is responsible for actually running _Nextcloud's_ upgrade process, so you'll find that this is the most important service to monitor. You can do that with:

    $ snap logs -f nextcloud.apache

Following the pattern we've already established, its error log is located at `/var/snap/nextcloud/current/logs/apache_errors.log`. If that looks good, then you should be able to visit Nextcloud at this time, but it might be in maintenance mode. That's because it's not the last step in the upgrade process-- check the Nextcloud Fixer.

## Nextcloud Fixer

The Nextcloud Fixer is the slick and creative name that I gave the service that works around all of Nextcloud's upgrade quirks and weirdnesses that we've run into over the years. For example, Nextcloud likes to introduce requirements in updates (new database columns, indices, primary keys) but then not take care of creating them for you. This is where we do such things. You can monitor that process with:

    $ snap logs -f nextcloud.nextcloud-fixer

It doesn't have a logfile of its own, so that command should give you everything.

## Conclusion

If all of the above services look healthy and you still can't visit the snap, something else is broken, probably not related to the snap. Check your surrounding infrastructure, your firewall, your port-forwarding etc., and make sure your IP address didn't change out from under you so your domain name isn't pointing to the wrong place.