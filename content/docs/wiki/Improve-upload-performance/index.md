---
date: '2026-07-16T21:01:29+02:00'
draft: true
title: 'Improve Upload Performance'
---
# Nextcloud upload chunk size

> **not recommended** for limited memory devices like Raspi2, Raspi3 and Raspi4 under 2Gib RAM and limited internet Bandwidth

## Adjust chunk size on Nextcloud side
[Nextcloud docs](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/big_file_upload_configuration.html#adjust-chunk-size-on-nextcloud-side)

For upload performance improvements in environments with high upload bandwidth, the server’s upload chunk size may be adjusted to double the default chunk size to (20MiB):

>Default is 10485760 (10 MiB).

```
sudo nextcloud.occ config:app:set files max_chunk_size --value 20971520
```

Changing max_chunk_size will **not** have any performance impact on files uploaded through **File Drop shares** as unauthenticated file uploads are not chunked.

## Large file upload on object storage
[Nextcloud docs](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/big_file_upload_configuration.html#large-file-upload-on-object-storage)

[Chunked file uploads](https://docs.nextcloud.com/server/latest/developer_manual/client_apis/WebDAV/chunking.html) do have a larger space consumption on the temporary folder when processing those uploads on object storage as the individual chunks get downloaded from the storage and will be assembled to the actual file on the Nextcloud servers temporary directory. It is recommended to increase the size of your temp directory accordingly and also ensure that request timeouts are high enough for PHP, webservers or any load balancers involved.

>[!TIP]
>
>In more recent versions of Nextcloud Server, when uploading to S3 in Primary Storage mode, we use S3 MultipartUpload.  This allows chunked upload streaming of the chunks directly to S3 so  that the final MOVE request no longer needs to assemble the final file  on the Nextcloud server. This requires your memcache.distributed  to be set to use Redis (or Memcached), otherwise we fall back on the  prior behavior which consumes space on the Nextcloud Server for file  assembly (as described above).
