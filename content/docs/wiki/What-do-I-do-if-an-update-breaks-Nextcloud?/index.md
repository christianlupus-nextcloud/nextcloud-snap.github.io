---
date: '2026-07-16T21:01:17+02:00'
draft: true
title: 'What Do I Do if an Update Breaks Nextcloud?'
---
Sorry you're in this situation! Don't worry though, the snap is designed with this scenario in mind, and this page will walk you through what you need to do.

There are a few reasons you might be reading this. The most common one is that the snap makes it really easy to skip major versions when updating, which is sadly not supported by Nextcloud. Another possibility is that an automatic update was applied and the new version removed or broke a feature on which you were relying.

First of all, let's get you back up and running. Snaps were designed to have a robust upgrade story. In the case of the Nextcloud snap, every time it refreshes (updates), it takes a snapshot of the database and other version-specific things (not the raw data though, e.g. pictures and documents) and holds them alongside the old revision of the snap. Then it runs all the database migrations and stuff in the new revision. If you skipped major versions, these migrations will fail, leading to a broken Nextcloud. Thankfully, all you need to do is say "yeah this revision of Nextcloud is broken, please get me back to where I was, with the snapshot of the database etc. from before those migrations were run." In snap parlance, that's called a "revert", and it looks like this:

    $ sudo snap revert nextcloud

With that command, you should be back up and running right at the moment _before_ the refresh happened. If that revision was broken in some way, you should [log an issue](https://github.com/nextcloud/nextcloud-snap/issues/new/choose). At this point, your next step depends on why you're here.

### My install was automatically updated (not to another major Nextcloud version) and broke in some way

Make sure you log the above-referenced issue, let's talk about it. Until then, rest assured that, because you reverted away from the broken update, it won't try to refresh to it again. However, if _another_ update is released it will try to refresh to _it_, so make sure to let us know if something is busted.

### My install was automatically updated to the next major Nextcloud version and that major version is broken in some way

Again, make sure you log an issue. Now that you've reverted, though, you should switch tracks for now to make sure Nextcloud doesn't try to update to that major version again until the breakage has been fixed. Let's say the major version that works is v18, and the one that's broken is v19. You just reverted from the broken v19 to the working v18 again. In order to make sure you stay on v18 until v19 is fixed, switch away from the stable channel to the major-version-specific track. In the case of this example, that's `18`:

    $ sudo snap refresh nextcloud --channel=18

### I didn't realize that I couldn't skip major versions when refreshing, and now my Nextcloud is broken

This shouldn't be possible these days due to some upgrade hooks we have in place, but we haven't always had that. For the sake of example, let's say you're on v16, and you tried to upgrade to v19. You see this plastered all over your logs (e.g. `journalctl -u snap.nextcloud.apache.service`):

> Exception: Updates between multiple major versions and downgrades are unsupported.
> Update failed

Right, so it's broken, and you revert after reading above: great. In order to properly update, we just need to walk through the major releases one by one, which is easy. Before we do that though, let's completely remove the v19 snap to which you upgraded, so that once we get to it after doing things in the proper order the data migrations can run afresh. To do that, you need to figure out the revision number for v19 like so:

    $ snap list nextcloud --all
    Name       Version       Rev    Tracking   Publisher   Notes
    nextcloud  16.0.11snap1  21459  19/stable  nextcloud✓  -
    nextcloud  19.0.0snap1   21796  19/stable  nextcloud✓  disabled

You'll see multiple revisions there, including the v16 revision you're on now as well as the (disabled) v19 revision from which you just reverted. In the example output above, the v19 revision is `21796`. To completely remove that revision, run the following command (**warning:** make sure you get this revision correct or you could remove v16, which would suck for you):

    $ sudo snap remove nextcloud --revision=21796
    nextcloud (revision 21796) removed

You can confirm that it's indeed gone:

    $ snap list nextcloud --all
    Name       Version       Rev    Tracking   Publisher   Notes
    nextcloud  16.0.11snap1  21459  19/stable  nextcloud✓  -

Alright, now let's refresh to each major Nextcloud version, in order, until we get to v19. First of all, the upgrade from v16 to v17:

    $ sudo snap refresh nextcloud --channel=17

You need to wait for this to complete running all the important migrations. You can tell when it's done because the web interface will come back. Take a quick poke around, make sure everything looks sane, then you can continue from v17 to v18:

    $ sudo snap refresh nextcloud --channel=18

Again, wait, then make sure it looks good. Then you can continue from v18 to v19:

    $ sudo snap refresh nextcloud --channel=19

And you're done! Note however, by staying on the `19` channel, you will never receive an update to v20. If you want to get back onto receiving automatic updates that include major versions, you need to continue the above process until you're upgraded enough you can switch back to the `stable` channel:

    $ sudo snap refresh nextcloud --stable