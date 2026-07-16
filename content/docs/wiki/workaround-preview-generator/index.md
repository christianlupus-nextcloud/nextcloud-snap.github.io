---
date: '2026-07-16T21:01:46+02:00'
draft: true
title: 'Workaround Preview Generator'
---
assuming the [preview generator app]( https://apps.nextcloud.com/apps/previewgenerator) is installed in your instance

[edit config.php](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#nextcloud-snap-editing-configuration-file) see also [PHP configuration options](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php)

```
'enable_previews' => true,
'enabledPreviewProviders' =>
 array (
    0 => 'OC\\Preview\\TXT',
    1 => 'OC\\Preview\\Image',
    2 => 'OC\\Preview\\Movie',
    3 => 'OC\\Preview\\MKV',
    4 => 'OC\\Preview\\MP4',
    5 => 'OC\\Preview\\AVI',
 ),
```

available options in the helpfile
![grafik](https://github.com/user-attachments/assets/a0fb6d53-672d-473b-aa77-46962807c1f1)

according to https://apps.nextcloud.com/apps/previewgenerator use the command:
```bash
sudo nextcloud.occ preview:generate-all -vvv
```

## Known issues, 'preview generator app'

> [!NOTE]
> The 'preview generator app' which is sometimes installed manually to generate previews for all sorts of media files gobbles up disk space blowing up the database and increasing CPU load. The "cached" files will want to be removed from the system drive to free up space. You can safely delete the `/var/snap/nextcloud/common/nextcloud/data/appdata_occredxxxx/preview/` directory **after uninstalling** the 'preview generator app'. The file previews will be regenerated with 'default nextcloud previews' without requiring the 'preview generator app' saving disk space and CPU load.

> [!IMPORTANT]
> Be patient, regenerating default previews may take a while depending on system resources, files quantity or external media to be scanned!

----

see also:

[**Database, apps and files maintenance**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/database%E2%80%90apps%E2%80%90files%E2%80%90maintenance#database-apps-and-files-maintenance)






