---
date: '2026-07-16T21:01:27+02:00'
draft: true
title: 'Configure Nextcloud Snap'
---
<p align="center" width="100%">
    <img width="15%" src="https://github.com/user-attachments/assets/e32a41fc-59ad-45db-8bcc-572363f8ab7b" alt="Nextcloud snap"> 
</p>
<h1 align="center">Nextcloud snap configuration</h1>

## HTTPS encryption with Lets Encrypt 

The only thing we recommend you do up front is enable HTTPS. Nextcloud snap **includes** a **service** for **automated HTTPS encryption** and **automated renewal** using Lets Encrypt, or self-signed certificates. Run `nextcloud.enable-https -h` for more information.

Enable Lets Encrypt in Nextcloud snap:

+ issue command in host shell

```bash
sudo nextcloud.enable-https lets-encrypt
```
Enter email address and domain/subdomain to get your SSL certificate.

> [!TIP]
> Read the resulting response carefully and ensure the requirements are met.
> 
> ![grafik](https://github.com/user-attachments/assets/01d50359-fa8c-40fa-8033-547bfd652457)
> 
> - [x] [network requirements](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Installation-requirements#network)
> - [x] [domain name and DNS](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Installation-requirements#domain-name-and-dns)

> [!IMPORTANT]
> Let's Encrypt will expect that Nextcloud is exposed on ports **80** and **443**. If you change ports and _don't_ put Nextcloud behind a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) such that ports 80 and 443 are sent to Nextcloud for that domain name, Let's Encrypt will be **unable** to **verify ownership** of your domain and will not grant certificates.
>
> Self-signed certificate is easier to setup than Let's Encrypt certificates, but will **cause warnings in browsers** and due to being very basic **won't work with some applications**.

* [Managing encryption](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-HTTP-encryption-(HTTPS))
* [HTTP/HTTPS port configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Port-configuration)
* [Reverse proxy configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy)
* [Hosts & FQDN configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)

----

## Removable media

The interface providing the ability to access removable media is not automatically connected upon install, so if you'd like to use external storage (or otherwise use a device in `/media` or `/mnt` for data), you need to give the snap permission to access removable media by connecting that interface:

+ issue command in host shell
```bash
sudo snap connect nextcloud:removable-media
```
> [!NOTE]
>Removable media can be any device (e.g. external-disk-partition, NFS-network mount, SSHFS-network mount etc.). 
The device should be available at system boot and mounted by `/etc/fstab`.

> [!TIP]
> Ensure USB-boot is disabled in BIOS or use the `--nofail` option in `/etc/fstab` for headless boot (i.e. server install), especially when connecting an external USB-device.

> [!TIP]
> Create a dedicated directory in `/media/MYDIRECTORY/` or `/mnt/MYDIRECTORY/` mounting the dedicated directory in `/etc/fstab` instead of Ubuntu's "automount" `/media/$USER/DEVICE/` directory.

> [!IMPORTANT]
> Removable media **must** be mounted to either `/media` or `/mnt` as **root** with **root** permissions and **connected** to Snap! 

* [Managing external media and storage](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-external-media,-shares-and-storage)
* [How to add samba shares and local shares](https://github.com/nextcloud-snap/nextcloud-snap/wiki/How-to-add-samba-shares-and-local-shares)
----

## System monitoring

The System application requires a bit more access to the system than the snap uses by default (e.g. the ability to monitor network hardware, etc.). If you'd like to utilize those features, you'll need to connect the interface that allows that kind of access:

+ issue command in host shell
```bash
sudo snap connect nextcloud:network-observe
```
----

## PHP Memory limit configuration

By default, PHP will use 128M as the memory limit. If you notice images not
getting previews generated, or errors about memory exhaustion in your Nextcloud
log, you may need to set this to a higher value.

If you'd like to set the memory limit to a higher value (say, 512M), run:

    sudo snap set nextcloud php.memory-limit=512M

To set it to be unlimited (not recommended), use -1:

    sudo snap set nextcloud php.memory-limit=-1

----

### Cronjob interval configuration

Nextcloud from 30.0.x recommends a 5 Minute cron interval:

By default the cronjob interval is 5 minutes.

<p align="center">
    <img src="https://github.com/user-attachments/assets/ab194b56-300b-4575-91c5-65e154027163" alt="first start">
</p>

To adjust it (to 5 minutes) simply run:

    $ sudo snap set nextcloud nextcloud.cron-interval=5m

To adjust it (to 10 minutes) run:

    $ sudo snap set nextcloud nextcloud.cron-interval=10m

If you want to disable the cronjob completely, run:

    $ sudo snap set nextcloud nextcloud.cron-interval=-1

To reenable it again simply set the `nextcloud.cron-interval` snap variable to a value that isn't `-1`

----

## HTTP compression configuration

By default, the snap does not enable HTTP compression. HTTP compression may improve site response where bandwidth and device resources are limited.

To enable it, run:

    sudo snap set nextcloud http.compression=true

To disable it, run:

    sudo snap set nextcloud http.compression=false

----

## Trusted domains configuration

By default, Nextcloud snap will accept HTTP requests addressed to localhost from the server itself. But when you're setting things up on a remote or virtual server (like an AWS EC2 instance) you’ll need to tell Nextcloud to expect remote browser requests made to your IP address or domain name. Note how you assign a separate ID number (iterating values 0, 1, 2...) for each domain. See [multiple trusted domains](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-trusted-domains) for `config.php` configuration options

Set trusted domains (iterating values 0, 1, 2...):

    sudo nextcloud.occ config:system:set trusted_domains 0 --value="cloud.yourdomain.com"
    sudo nextcloud.occ config:system:set trusted_domains 1 --value="cloud.yourseconddomain.com"
    sudo nextcloud.occ config:system:set trusted_domains 2 --value="cloud.yourotherdomain.com"

See [setting trusted domains](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-trusted-domains).

----

## Trusted proxy configuration

When you're running Nextcloud snap behind a proxy the snap needs to know about the proxy. Add [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) to trusted proxies. See [Nextcloud reverse proxy documentation](https://docs.nextcloud.com/server/27/go.php?to=admin-reverse-proxy).

Set trusted proxies (iterating values 0, 1, 2...):

    sudo nextcloud.occ config:system:set trusted_proxies 0 --value="your.reverse.proxy.ip"
    sudo nextcloud.occ config:system:set trusted_proxies 1 --value="your.other.proxy.ip"
    sudo nextcloud.occ config:system:set trusted_proxies 2 --value="another.reverse.proxy.ip"

See [setting trusted proxies](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-trusted-proxies)

----

## Built-in CODE Server and Nextcloud office configuration

See [configure Collabora Online - Built-in CODE Server and Nextcloud office on Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-CODE-and-Nextcloud-office-for-Nextcloud-snap)

----

## Configuration options

* [PHP configuration options](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php)

* [Port configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Port-configuration)

* [Change data directory](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Wiki-Change-data-directory)

* [Hosts & FQDN configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)

* [HPB client-push configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-HPB-client-push-for-Nextcloud-snap)

## Further options

* [Putting Nextcloud snap behind a reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy)

* [Let NGINX proxy manager handle encryption](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager)
