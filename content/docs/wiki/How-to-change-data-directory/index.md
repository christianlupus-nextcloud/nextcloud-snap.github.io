---
date: '2026-07-16T21:01:41+02:00'
draft: false
title: 'How to Change Data Directory'
---
## How-to change data directory by r00ta

User [r00ta](https://help.nextcloud.com/t/how-to-change-datadir-in-nextcloud-snap/205210) posted this quick and simple how to:

...install nextcloud with snap 

```bash
sudo snap install nextcloud 
```
then
```bash
sudo nextcloud.occ maintenance:install --data-dir <YOUR PATH> --admin-user <YOUR USERNAME>
```
depending on your setup and finally
```bash
sudo nextcloud.occ maintenance:mode --off
```

