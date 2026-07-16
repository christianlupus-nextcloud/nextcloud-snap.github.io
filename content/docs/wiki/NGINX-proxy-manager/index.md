---
date: '2026-07-16T21:01:10+02:00'
draft: false
title: 'NGINX Proxy Manager'
---
## Reverse proxy with termination

Let [NGINX proxy manager](https://nginxproxymanager.com/) handle encryption and certificate management for Nextcloud snap.
This setup will assume that SSL termination and certificate management is handled by NGINX proxy manager.

The Nextcloud snap encryption and certificate renewal services may be [disabled](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-services#disable-nextcloud-snap-service). 

* Stop and disable encryption:

```
sudo nextcloud.disable-https lets-encrypt
```

* Stop and disable certificate renewal service:

```
sudo snap stop --disable nextcloud.renew-certs
```

----
## NGINX proxy manager settings

* ### *Proxy Host* 

![grafik](https://user-images.githubusercontent.com/54933878/219947858-9756dc45-1c29-4145-b8c4-cc080b4147d1.png)

* **Cache Assets** (_optional_) --> *_recommend disabled_*
>Tip: caching assets may be required in some cases, disabling caching assets is recommended for most
* **Websockets Support** (_optional_)
* **Block Common Exploits** (_optional_)


----
* ### *SSL* configuration

![grafik](https://user-images.githubusercontent.com/54933878/219947921-f4373ba0-3e3b-4f35-92e5-d894da396a1e.png)

* **HSTS Subdomains** (_optional_)

----
* ### *Advanced* settings for Nextcloud snap

![grafik](https://user-images.githubusercontent.com/54933878/219947752-5886d1a8-c7e0-4a82-9e31-7b72ba865b3f.png)

### Setting required!! prior to NC29

copy & paste into advanced settings
```
location = /.well-known/carddav {return 301 $scheme://$host:$server_port/remote.php/dav;}
location = /.well-known/caldav {return 301 $scheme://$host:$server_port/remote.php/dav;}
```
### Setting required!! from NC29
>Ref: [upstream issue](https://github.com/nextcloud/server/issues/45033)

copy & paste into advanced settings
```
location /.well-known/carddav {return 301 $scheme://$host/remote.php/dav/;}
location /.well-known/caldav {return 301 $scheme://$host/remote.php/dav/;}
```
**_optional setting from NC29_**
>Tip: recommend only if recurring errors appear in logs
* _resolve webfinger error in logs_
* _resolve nodeinfo error in logs_
```
location /.well-known/webfinger {return 301 $scheme://$host/index.php/.well-known/webfinger;}
location /.well-known/nodeinfo {return 301 $scheme://$host/index.php/.well-known/nodeinfo;}
```
**_optional tweaks_**
```
location /.well-known/carddav {return 301 $scheme://$host/remote.php/dav/;}
location /.well-known/caldav {return 301 $scheme://$host/remote.php/dav/;}
location ^~ /.well-known {return 301 $scheme://$host/index.php$uri;}
client_body_buffer_size 512k;
proxy_read_timeout 86400s;
client_max_body_size 0;
```

## Configure Nextcloud snap for reverse proxy

### Trusted proxies in Nextcloud snap
 * Set [**trusted proxies**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-trusted-proxies) in [config-php](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php) if your Nextcloud snap instance is behind a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy#nginx-optional-custom-path-location-for-reverse-proxy). 
```
  'trusted_proxies' => 
  array (
    0 => 'your.reverse.proxy.ip',
    1 => 'your.other.proxy.ip',
   ),
```
or issue command on host (iterating values 0, 1, 2...):

    sudo nextcloud.occ config:system:set trusted_proxies 0 --value="your.reverse.proxy.ip"

## FQDN for Nextcloud snap instance, vm or container behind reverse proxy with termination

Setting FQDN (fully qualified domain name) is **best practice** and is **optional** on self-hosted Nextcloud snap instances running on vm or container behind reverse proxy **with termination** and may improve site response. 

* Discover hostname: 
  - issue command 
```
 hostnamectl
``` 
 or 
```
 hostname
```

* Set or change hostname: 
  - issue command 
``` 
hostnamectl set-hostname NEW-HOSTNAME
```

* Set FQDN on instance behind reverse proxy **with** termination: 
  - edit `/etc/hosts` in vm or container using your favourite editor as root `$ sudo nano /etc/hosts` and add second line
 replacing `<your.reverse.proxy.ip> <your.domain.xyz>` and `<hostname>` with your own **domain name** and **hostname**:

```
127.0.0.1       localhost
<your.reverse.proxy.ip>   <your.domain.xyz>   <hostname>

## The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

----
## Proxy options

* [Putting Nextcloud snap behind a reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy)
* [Hosts and FQDN](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)