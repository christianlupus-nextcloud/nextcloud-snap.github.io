---
date: '2026-07-16T21:01:28+02:00'
draft: false
title: 'How to Resolve Richdocuments Boot Error'
---
# resolve richdocuments `boot error` and `unklink` in Nextcloud snap logs

https://github.com/nextcloud/richdocuments/issues/3780

Error in Nextcloud logs:
```
"Could not boot richdocuments: OCA\\Richdocuments\\Service\\CachedRequestService::get(): 
```
and
```
[PHP] Error: unlink(/var/www/nextcloud/data/appdata_ocgfs89mpg4c/richdocuments/remoteData/capabilities): No such file or directory at /var/www/nextcloud/lib/private/Files/Storage/Local.php#348
	GET /ocs/v2.php/apps/notifications/api/v2/notifications?format=json
```

Resolution: https://github.com/nextcloud-snap/nextcloud-snap/issues/2901

```bash
sudo /snap/nextcloud/current/bin/redis-cli -s /tmp/snap-private-tmp/snap.nextcloud/tmp/sockets/redis.sock
```

```
FLUSHDB
```
![374545778-f1123ae3-68bf-4fbb-976d-6870958785b2](https://github.com/user-attachments/assets/3ca80b92-123f-4abd-b96e-bb148a08076e)

error in log disappears! ✔