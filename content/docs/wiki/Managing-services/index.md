---
date: '2026-07-16T21:01:47+02:00'
draft: false
title: 'Managing Services'
---
<h1 align="center">Managing Nextcloud snap services</h1>

## Documentation

[Snapcraft documentation](https://snapcraft.io/docs/service-management)

---

## View Nextcloud snap processes in htop

```bash
htop -F nextcloud
```

## View Snap services for Nextcloud snap

```text
sudo snap services <SNAPNAME>
```

Example:

```bash
sudo snap services nextcloud
```

## View service-logs for Nextcloud snap

```text
sudo snap logs <SNAPNAME>.<SERVICE>
```

Example:

```bash
sudo snap logs nextcloud.nextcloud-cron
```

## Journalctl service logs

example of service logs:

Logs for Apache service: 

```bash
journalctl -u snap.nextcloud.apache.service
```

Logs for Mysql service:  

```bash
journalctl -u snap.nextcloud.mysql.service
```

## Manage Nextcloud snap service using `get` and `set`

**View configuration with `get`:**

```bash
sudo snap get <SNAPNAME> <SERVICE>-<SETTING>
```

Example:

```bash
sudo snap get nextcloud nextcloud.cron-interval
```

**Configure service with `set`:**

```text
sudo snap set <SNAPNAME> <SERVICE>.<SETTING>=<value>
```

Example:

```bash
sudo snap set nextcloud nextcloud.cron-interval=5m
```

---

## Start Nextcloud snap service

```text
sudo snap start <SNAPNAME>.<SERVICE>
```

Example:

```bash
sudo snap start nextcloud.nextcloud-cron
```

## Restart Nextcloud snap service

```text
sudo snap restart <SNAPNAME>.<SERVICE>
```

Example:

```bash
sudo snap restart nextcloud.nextcloud-cron
```

## Stop Nextcloud snap service

```text
sudo snap stop <SNAPNAME>.<SERVICE>
```

Example:

```bash
sudo snap stop nextcloud.nextcloud-cron
```

## Disable Nextcloud snap service

```text
sudo snap stop --disable <SNAPNAME>.<SERVICE>
```

Example:

```bash
sudo snap stop --disable nextcloud.renew-certs
```

## Enable Nextcloud snap service

```text
sudo snap start --enable <SNAPNAME>.<SERVICE>
```

Example:

```bash
sudo snap start --enable nextcloud.renew-certs
```