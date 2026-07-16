---
date: '2026-07-16T21:01:16+02:00'
draft: false
title: 'Export Nextcloud User Address Book'
---
# Export/Backup Nextcloud USER address book

Nextcloud address books are stored in the nextcloud database per user. There is currently no known occ-command to export user address book.
This can be accomplished using a `curl` command. 

The user address book is exported as `*.vcf` file to the current directory:

```bash
 curl -L -J -o -u "<USER>:<PASSWORD>" "https://cloud.domain.tld/remote.php/dav/addressbooks/users/<USER>/<addressbookname>/?export"
```

Example script for automated cronjob defining the output directory:

```bash
 #!/bin/bash
#################################################################################
# NC-contacts-export  -scubamuc- https://scubamuc.github.io/                  #
#################################################################################
## Nextcloud addressbook export
## create cronjob to backup contacts weekly on Saturday at 22:00
##  0 22 * * 6 su - <$USER> /home/$USER/bin/NC-contacts-export.sh
#################################################################################
             #  Nextcloud contacts export #
 
 curl -L -J -u "<USER>:<PASSWORD>" "https://cloud.domain.tld/remote.php/dav/addressbooks/users/<USER>/<addressbookname>/?export" 
 --output /home/$USER/Backup/contacts-backup$(date '+%Y%m%d_%T').vcf

```