---
date: '2026-07-16T21:01:11+02:00'
draft: false
title: 'How to Configure Collabora CODE With Docker'
---
# How to configure Collabora CODE with docker for Nextcloud snap

## Please prefer the [Nextcloud community how-to](https://help.nextcloud.com/t/install-configure-collabora-code-on-nextcloud-with-docker/216725)

> [!NOTE]
>**Be aware, this how to is as-is and is not supported by the Nextcloud snap team!**

* [CODE Docker image — SDK https://sdk.collaboraonline.com/ documentation](https://sdk.collaboraonline.com/docs/installation/CODE_Docker_image.html)
* [Configuration — SDK https://sdk.collaboraonline.com/ documentation](https://sdk.collaboraonline.com/docs/installation/Configuration.html#multihost-configuration)

This example will require [Docker](https://www.docker.com/) and a [Reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) for the CODE server.

Install Docker on host. Create a DNS for subdomain like `office.mydomain.tld`. Create a proxy host for **http** on port 9980 on reverse proxy pointing to that host and get the domain name encrypted.

## Docker Stack:

```
name: 'code'

services:
  collabora:
    image: collabora/code:latest
    container_name: collabora
    environment:
      - aliasgroup1=https://cloud.mydomain.tld:443,https://cloud\\.mydomain\\.tld:443 # enable for aliasgroup1
      # - aliasgroup2=https://cloud.otherdomain.tld:443,https://cloud\\.otherdomain\\.tld:443 # enable for aliasgroup2
      # - aliasgroup3=https://cloud.somedomain.tld:443,https://cloud\\.somedomain\\.tld:443 # enable for aliasgroup3
      - username=admin
      - password=********         # Replace with a strong password
      - dictionaries=en_GB en_US de_DE
      - extra_params=
      --o:ssl.enable=false 
      --o:ssl.termination=true
      --o:logging.level=warning
      --o:user_interface.mode=compact
    ports:
      - "9980:9980"
    restart: always
```
> [!TIP]
> `- extra_params=--o:ssl.enable=false --o:ssl.termination=true` disabling ssl in coolsweed/collabora and forwarding **http** in reverse proxy handling ssl termination.
>
> `- aliasgroup` iterating 1,2,3 for multiple domains. IMPORTANT: be aware of formatting conventions using `\\` as separator before `.`
>
> `- dictionaries` add dictionaries space seperated `en_US en_GB de_DE` etc.
>
> WOPI is not necessary when defining aliasgroups as allowed hosts
>

## Collabora Docker options (multiple nextcloud clients)

Each `aliasgroup` represents the allowed "client" domain, which will prevent unregistered clients from accessing the CODE server. Thus using aliasgroups resolves the issue of allowed WOPI client IP's.

```
- aliasgroup1=https://cloud.mydomain.tld:443,https://cloud\\.mydomain\\.tld:443
- aliasgroup2=https://cloud.otherdomain.tld:443,https://cloud\\.otherdomain\\.tld:443
- aliasgroup3=https://cloud.somedomain.tld:443,https://cloud\\.somedomain\\.tld:443
```
## Collabora Docker parameters

### optional extra parameters etc. refer to the docs
```
    -extra_params=
        --o:ssl.enable=false disable ssl termination in coolswd/collabora
        --o:ssl.termination=true forwarding http to reverse proxy handling ssl termination
        --o:mount_jail_tree=true
        --o:home_mode.enable=true
        --o:logging.level_startup=warning
        --o:logging.disable_server_audit=true

```

## Dictionaries

```
- dictionaries=en_GB en_US de_DE
```

## Nextcloud Office Statistics and admin interface

```
https://office.mydomain.xyz/browser/dist/admin/admin.html
```
## Reverse proxy

* [Proxy settings](https://sdk.collaboraonline.com/docs/installation/Proxy_settings.html)

### Reverse proxy manager config example 

Be aware that you are forwarding **http** and **Websockets support** (WSS) only!

![grafik](https://github.com/user-attachments/assets/9c891779-dc78-4f9f-ab1f-d5bd3a762d29)

----

## How to install and configure Collabora Online on a separate server for Nextcloud Snap

https://github.com/yannicklescure/wiki-md/blob/main/nextcloud/install-configure-collabora-online-nextcloud-snap-server.md