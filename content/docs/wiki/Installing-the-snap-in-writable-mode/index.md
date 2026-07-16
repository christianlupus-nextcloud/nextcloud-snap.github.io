---
date: '2026-07-16T21:01:24+02:00'
draft: false
title: 'Installing the Snap in Writable Mode'
---
The snap is by definition read-only, but some times it's useful to poke at the contents of it to see what's going on. If we want to do that, there are some hoops we can jump through to get there. **Note that this is not recommended for production use.** The snap won't be as secure and will no longer update if you do this. This is handy for development/experimentation, nothing more.

I'm runnning these commands on a fully up-to-date Ubuntu 18.04, they are untested elsewhere. I know from experience that this does not work with home disk encryption (ecryptfs), but full disk encryption is fine.

### Download the snap in question

This will download both the snap and the store assertion (store signature for the snap). You can optionally specify the channel, it will default to using stable:

    $ snap download nextcloud [--channel=14/candidate]

### Unpack the snap

We're doing this so that we can modify it (otherwise we wouldn't be able to write to the squashfs image):

    $ unsquashfs nextcloud_10079.snap

That unpacks by default to a directory named "squashfs-root".

### Install the snap in read/write mode

We can use `snap try` to install the snap in read/write mode now that we've unpacked it:

    $ sudo snap try squashfs-root

### Use nextcloud and tinker away

The snap will fire up after the `snap try` command and start listening on port 80, so you can just visit `http://localhost` in your browser to create an admin user and get up and running. The Nextcloud sources are in `squashfs-root/htdocs/`, although note that the config being used is not in there, but in `/var/snap/nextcloud/current/nextcloud/config/`.

### Remove nextcloud when done

When you're satisfied with your tinkering, you can uninstall the Nextcloud snap like normal, getting rid of the config and all data:

    $ sudo snap remove nextcloud