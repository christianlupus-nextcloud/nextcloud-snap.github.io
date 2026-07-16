---
date: '2026-07-16T21:01:35+02:00'
draft: false
title: 'Managing Cron'
---
<h1 align="center">Managing Nextcloud snap cron</h1>

## View `cron` configuration with "get"
```
sudo snap get nextcloud nextcloud.cron-interval
```
## Set `cron` configuration with "set"

[Nextcloud documentation](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/background_jobs_configuration.html)

Nextcloud recommends a 5 Minute interval:

![grafik](https://github.com/user-attachments/assets/ab194b56-300b-4575-91c5-65e154027163)

value =5m in minutes
```
sudo snap set nextcloud nextcloud.cron-interval=5m
```

## Restart `cron` manually 
```
sudo snap restart nextcloud.nextcloud-cron
```

### If `cron` fails after update
```
sudo nextcloud.occ background:cron
```

### Disable `cron`
```
sudo snap set nextcloud nextcloud.cron-interval=-1
```

### Enable `cron`

value =5m in minutes
```
sudo snap set nextcloud nextcloud.cron-interval=5m
```

## Cronjob interval configuration

By default the cronjob interval up until Nextcloud 29.0.x was 15 minutes. Nextcloud from 30.0.x recommends a 5 Minute interval:

![grafik](https://github.com/user-attachments/assets/ab194b56-300b-4575-91c5-65e154027163)

To adjust it (to 5 minutes) simply run:

    $ sudo snap set nextcloud nextcloud.cron-interval=5m

If you want to disable the cronjob completely, run:

    $ sudo snap set nextcloud nextcloud.cron-interval=-1

To reenable it again simply set the `nextcloud.cron-interval` snap variable to a value that isn't `-1`


----

## Managing cron on the host

[The complete cron job guide](https://cronitor.io/guides/cron-jobs?utm_source=crontabguru&utm_campaign=cron_reference&utm_content=21)

### View root crontab on host 
```bash
sudo crontab -l
```
### Edit root crontab on host 
```bash
sudo crontab -e
```
### Add root cronjob as root 
```bash
* * * * * nextcloud.occ <function>:<command> <--option>
```

>**Examples**:
>
>* scan files `* * * * * nextcloud.occ files:scan --all`
>
>* preview generator `* * * * * nextcloud.occ preview:generate-all -vvv`
>
>* clean trash  `* * * * * nextcloud.occ trashbin:cleanup --all-users`

### Add root cronjob as user 
>best practice to run user scripts requiring root privileges as cronjob
```bash
* * * * * su <USER> /home/<USER>/path/to/script.sh
```

## Required schedule expression
see https://crontab.guru/ 
