---
date: '2026-07-16T21:01:22+02:00'
draft: false
title: 'How to Backup Your Instance'
---
The nextcloud snap includes an utility to backup and restore your instance. Right now it can export:
* The apps
* The database
* The config
* The raw data

But note that this does not cover exporting other settings, like HTTPS certificates.

# Export everything
Run sudo `nextcloud.export` and it'll turn on maintenance mode and dump a backup of your entire instance into a timestamped folder, something like `20190121-153921`, inside `/var/snap/nextcloud/common/backups/`.

After the export is complete the snap will forget about it, so feel free to do whatever you like with it; one option could be to tar it, with a command like this:

`sudo tar -cvf 20190121-153921_nextcloud-backup.tar /var/snap/nextcloud/common/backups/20190121-153921`

# Import everything
You can import an exported backup by running `sudo nextcloud.import path/to/backup/dir`.

**Important!**
Importing a backup will completely blow away the current instance: it'll drop the database, delete the installed apps, and delete all raw data, replacing each with that from the backup.


The only constraints are that:
* This path needs to be available from confinement, so put it in `/var/snap/nextcloud/common` or `/var/snap/nextcloud/current/` somewhere.
* User `root` needs to be the owner.

So continuing with the previous example, these two commands would need to be executed before importing:

`sudo mv 20190121-153921_nextcloud-backup.tar /var/snap/nextcloud/current/`

`sudo chown -R root:root /var/snap/nextcloud/current/20190121-153921/`

# Selective backup
Note that you can limit what is exported (and what is imported) with arguments that are documented if you run `nextcloud.export -h` or `nextcloud.import -h`.

## Example

```
Usage:
    nextcloud.export [OPTIONS]
    Export data suitable for migrating servers. By default this
    includes the Nextcloud database, configuration, and data
    (equivalent to running nextcloud.export -abcd).

Available options:
    -h: Display this help message
    -a: Include the (non-default) apps
    -b: Include the database
    -c: Include the config
    -d: Include the data (can be quite large)
```

# Tips & tricks
Q. Can exporting specify the data directory?  
A. Not directly, but you should be able to use a symlink. For example, creating a symlink from the data directory to `/var/snap/nextcloud/common/nextcloud/data`

Q. Can I somehow export/import my HTTPS certs?  
A. Yes, everything related to them is stored in the `/var/snap/nextcloud/current/certs` directory, so copying it and preserving its permissions and owner works.

# Caveats

The export/import tools are mostly written from the perspective of wanting to migrate your current install to another instance of the snap. Keep that in mind:

- It won't be particularly helpful in migrating to the snap from a non-snap install
- Likewise, it won't be particularly helpful in migrating away from the snap
- It creates a copy of your config, trusted domains and all. If you're migrating to a new domain name, keep this in mind-- you'll need to update the trusted domains yourself.

----
See also [Backup, restore & migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore)