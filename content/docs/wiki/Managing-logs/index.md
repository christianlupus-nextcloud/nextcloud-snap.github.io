---
date: '2026-07-16T21:01:59+02:00'
draft: false
title: 'Managing Logs'
---
<h1 align="center">Managing Nextcloud snap logs</h1>

In the event of an issue the Nextcloud logs could help figure out a probable cause. Most activities and actions in Nextcloud are logged. An automated log rotation mechanism within Nextcloud clears and retains the logs regularly to prevent an overflow. 

> [!TIP] 
> Apps for viewing logs:
> `less` is `more`, from `head` to `tail` with `cat` or `bat` but `lnav` is the way to go!
> ```
> sudo lnav /var/snap/nextcloud/current/logs/nextcloud.log
> ```

## Watch Nextcloud snap log (live)
```
sudo nextcloud.occ log:watch
```

## Truncate logs
```
sudo truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log
```

## Nextcloud snap log locations

* Logs for Nextcloud: `/var/snap/nextcloud/current/logs/nextcloud.log`
  * _Note that this won't be the case if you've configured a custom_ `data` _directory, for example somewhere in _`/media`_. The nextcloud logs will then be stored on that same directory._
* Logs for Apache2:   `/var/snap/nextcloud/current/logs/apache_access.log`
* Logs for Redis:     `/var/snap/nextcloud/current/logs/redis.log`
* Logs for PHP-FPM:   `/var/snap/nextcloud/current/logs/php-fpm_errors.log`
* Logs for PHP:       `/var/snap/nextcloud/current/logs/php_errors.log`
* Logs for Certbot:   `/var/snap/nextcloud/current/certs/certbot/logs/letsencrypt.log`
* Logs for MySQL:     `/var/snap/nextcloud/current/logs/mysql_errors.log`

## Nextcloud snap service logs

[@Kyrofa's blog](https://kyrofa.com/posts/monitoring-a-nextcloud-snap-upgrade/)

> Nextcloud snap has several services contained within it. There are four that are most important for upgrade purposes:
> 
>  +  MySQL (nextcloud.mysql)
>  +  PHP (nextcloud.php-fpm)
>  +  Apache (nextcloud.apache)
>  +  Nextcloud Fixer (nextcloud.nextcloud-fixer)

### MySQL log
```
sudo snap logs -f nextcloud.mysql
```

### PHP log
```
sudo snap logs -f nextcloud.php-fpm
```

### Apache log
```
sudo snap logs -f nextcloud.apache
```

### Nextcloud Fixer log
```
sudo snap logs -f nextcloud.nextcloud-fixer
```

## Issue debugging script
[Debugging script](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Issue-Debugging-Scripts#debugging-script)