---
date: '2026-07-16T21:01:52+02:00'
draft: false
title: 'Database‐apps‐files‐maintenance'
---
# Database, apps and files maintenance

at times it may be necessary to tidy up files, apps and the database in your Nextcloud snap.
Especially when some third party app has caused issues with the snap and left a "mess" behind. 
These commands in sequence will free up space and shrink the database, see [comment](https://github.com/nextcloud-snap/nextcloud-snap/issues/2758#issuecomment-2143605231)


> **Example Previewgenerator app**
>
> The `previewgenerator app` is sometimes installed manually by the user beside 'default nextcloud previews' to generate previews for all sorts of media files. It gobbles up disk space blowing up the database and increasing CPU load breaking the Nextcloud snap cron service. **After removing** the `previewgenerator app` by issuing the command `sudo nextcloud.occ app:remove previewgenerator` you can safely delete the `/var/snap/nextcloud/common/nextcloud/data/appdata_occredxxxx/preview/` cache directory. The default file previews will be regenerated without requiring the `previewgenerator app` saving disk space and CPU load.
>
> The "cached" files will want to be removed from the system drive to free up space. You will need to [rescan and cleanup](https://github.com/nextcloud-snap/nextcloud-snap/wiki/database%E2%80%90apps%E2%80%90files%E2%80%90maintenance#rescan-and-cleanup) files and apps after removing the `previewgenerator app`, and sometimes the database too.
>
> Be patient, regenerating default previews may take a while depending on system resources, files quantity or external media to be scanned!

----


## rescan and cleanup  

### 1. repair database tree
```bash
sudo nextcloud.occ files:repair-tree
```

### 2. rescan files for all users
```bash
sudo nextcloud.occ files:scan --all
```

### 3. rescan appdata
```bash
sudo nextcloud.occ files:scan-app-data
```

### 4. cleanup orphaned entries
```bash
sudo nextcloud.occ files:cleanup
```

> [!TIP]
> Maintenance actions can be scripted. This is **optional** as Nextcloud has maintenance routines which work fine.
> ```bash
>#!/bin/bash
>echo "SCRIPT TO MAKE NEXTCLOUD FILE SCAN NEW EXTERNAL STORAGE AND PERFORM SOME MAINTENANCE";
>echo "1. RESCAN CHANGES AND CLEANUP";
>sudo nextcloud.occ files:repair-tree -n -vvv;
>sudo nextcloud.occ files:scan --all -n -vvv;
>sudo nextcloud.occ files:scan-app-data -n -vvv;
>sudo nextcloud.occ files:cleanup -n -vvv;
>```

----

# Database, clear 'undo' history

Nextcloud Snap MySQL keeps an 'undo' history (`temp_undo_00x.ibu`) which may become duplicated and grow over time gobbling up disk space. The MySQL 'undo' files in `/var/snap/nextcloud/current/mysql` may need clearing...

> [!WARNING]
> This is like open heart surgery... be **safe** and **backup** your database!
> ```
> sudo nextcloud.export -d
> ```

> [!CAUTION]
> Access the database with the mysql client:
> ```bash 
> sudo nextcloud.mysql-client
>```
>
> and run the below commands
> 
> ```bash
> use nextcloud;
> 
> CREATE UNDO TABLESPACE temp_undo_003 ADD DATAFILE 'temp_undo_003.ibu';
> 
> ALTER UNDO TABLESPACE innodb_undo_001 SET INACTIVE;
> SELECT NAME, STATE FROM INFORMATION_SCHEMA.INNODB_TABLESPACES WHERE NAME = 'innodb_undo_001';
> ALTER UNDO TABLESPACE innodb_undo_001 SET ACTIVE;
> 
> ALTER UNDO TABLESPACE innodb_undo_002 SET INACTIVE;
> SELECT NAME, STATE FROM INFORMATION_SCHEMA.INNODB_TABLESPACES WHERE NAME = 'innodb_undo_002';
> ALTER UNDO TABLESPACE innodb_undo_002 SET ACTIVE;
> 
> ALTER UNDO TABLESPACE temp_undo_003 SET INACTIVE;
> DROP UNDO TABLESPACE temp_undo_003;
> ```



