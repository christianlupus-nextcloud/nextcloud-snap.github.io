---
date: '2026-07-16T21:01:20+02:00'
draft: true
title: 'Port Configuration'
---
<h1 align="center">HTTP and HTTPS port configuration</h1>

By default, the snap will listen on port 80. If you enable HTTPS, it will listen
on both 80 and 443, and HTTP traffic will be redirected to HTTPS. But perhaps
you're [putting the snap behind a proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) of some kind, in which case you probably
want to change those ports.

If you'd like to change the HTTP port (say, to port 81), run:

    sudo snap set nextcloud ports.http=81

To change the HTTPS port (say, to port 444), run:

    sudo snap set nextcloud ports.https=444

Note that, assuming HTTPS is enabled, this will cause HTTP traffic to be
redirected to port 444. You can specify both of these simultaneously as well:

    sudo snap set nextcloud ports.http=81 ports.https=444

> [!IMPORTANT]
> Let's Encrypt will expect that Nextcloud is exposed on ports 80 and 443. If you change ports and _don't_ put Nextcloud behind a reverse proxy such that ports 80 and 443 are sent to Nextcloud for that domain name, Let's Encrypt will be unable to verify ownership of your domain and will not grant certificates.

> [!NOTE]
> Nextcloud's automatic hostname detection can fail when behind a proxy; you might notice it redirecting incorrectly. If this happens, override the automatic detection (including the port if necessary), e.g.:
>
> ```
>    sudo nextcloud.occ config:system:set overwritehost --value="example.com:81"
> ```