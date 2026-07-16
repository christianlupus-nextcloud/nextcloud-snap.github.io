---
date: '2026-07-16T21:01:31+02:00'
draft: false
title: 'Configure Nextcloud Office, Euro Office for Nextcloud Snap With Docker'
---
# Nextcloud Office -- Euro Office for Nextcloud snap with Docker

> [!NOTE]
>**Be aware, this how to is as-is and is not supported by the Nextcloud snap team!**

* official documentation
https://help.nextcloud.com/t/how-to-install-euro-office-in-nextcloud/246445

<p align="center" width="100%">
    <img width="15%" src="https://github.com/user-attachments/assets/e32a41fc-59ad-45db-8bcc-572363f8ab7b" alt="Nextcloud snap">
</p>

## installing Nextcloud Office -- Euro office for Nextcloud snap with Docker

This example will require [Docker](https://www.docker.com/) and a [Reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) for the Euro-Office document server.

Install Docker on host. Create a DNS for subdomain like `eurooffice.mydomain.tld`. Create a proxy host for HTTP & WSS (Websockets Support) for port `8890` on reverse proxy pointing to that host and get the domain name encrypted.

<p align="center" width="100%">
    <img width="50%" src="https://github.com/user-attachments/assets/3880e702-d406-4fed-8ef5-431b0fd16c97" alt="Euro Office -- Nextcloud snap">
</p>

### Create a random **32 character** password with `openssl` for that service;

> [!IMPORTANT]
>
> Make sure you create a `JWT_SECRET` (32 char) **for that service**! **Note**: the document server will fail to start correctly without a **32 character** `JWT_SECRET`!
> Note down the `JWT_SECRET` as you will need it for creating the Docker stack and for configuring Nextcloud Office.
> * issue command in host shell
>```bash
>openssl rand -hex 32
>```
>
>    <img width="50%" src="https://github.com/user-attachments/assets/ba52530d-ed98-4857-a224-fb969be28a8d" alt="JWT_SECRET">

### create and launch Docker stack

```
services:
    euro-office:
        container_name: euro-office
        image: ghcr.io/euro-office/documentserver:latest
        restart: unless-stopped
        environment:
            - JWT_ENABLED=true
            - JWT_SECRET=9316adec2dd1c83cdab77777d75424e2fd000957h386ba45k92e6860g1713403
        ports:
            - 8890:80
        volumes:
            - euro-office_data:/var/lib/euro-office
            - euro-office_config:/etc/euro-office
            - euro-office_logs:/var/log/euro-office
            - ./custom-fonts:/usr/share/fonts/custom
        tty: true
        stdin_open: true

volumes:
    euro-office_data:
    euro-office_config:
    euro-office_logs:
```

## Install Nextcloud Office connector on Nextcloud snap

* Install "Nextcloud Office" from app store in Nextcloud

or

* issue in commandline:
```
sudo nextcloud.occ app:enable eurooffice
```
## configure Nextcloud Office in Nextcloud

* enter your documentserver URL `https://eurooffice.mydomain.tld`
* enter secret key (JWT_SECRET as defined in docker stack `9316adec2dd1c83cdab77777d75424e2fd000957h386ba45k92e6860g1713403`)

<p align="center" width="100%">
   <img width="50%" src="https://github.com/user-attachments/assets/592779ef-6b50-4fbd-bea5-9ace9d7cfcf7" alt="configure eurooffice">
</p>





