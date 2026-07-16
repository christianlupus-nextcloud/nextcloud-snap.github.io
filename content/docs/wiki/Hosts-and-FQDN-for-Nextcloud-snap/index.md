---
date: '2026-07-16T21:01:58+02:00'
draft: false
title: 'Hosts and FQDN for Nextcloud Snap'
---
<h1 align="center">Hosts & FQDN configuration</h1>

## FQDN (**F**ully **Q**ualified **D**omain **N**ame)

A fully qualified domain name (a.k.a *absolute domain name*) is a complete address that specifies its exact location in the internet’s domain name system [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) and resolves a “human readable domain name” to an IP address. It includes the **hostname** and the **domain** name, which uniquely identify a specific server on the internet. This specifies all domain levels, including the top-level domain (**TLD**), in **hostname**.**domain**.**tld** format.


## FQDN for Nextcloud snap on internet facing instance

Setting FQDN (**F**ully **Q**ualified **D**omain **N**ame) is **best practice** and is **optional** on self-hosted internet facing Nextcloud snap instance and may improve site response. 

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

* Set FQDN on internet facing device: 
  - edit `/etc/hosts` on internet facing host using your favourite editor as root `$ sudo nano /etc/hosts` and add second line replacing `<your.domain.xyz>` and `<hostname>` with your own **domain name** and **hostname**:
```
127.0.0.1       localhost
127.0.1.1       <your.domain.xyz>   <hostname>

## The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

## FQDN for Nextcloud snap instance on internet facing vm or container

Setting FQDN (**F**ully **Q**ualified **D**omain **N**ame) is **best practice** and may be required on some hosted vm's or containers, but **optional** on self-hosted vm's or containers running Nextcloud snap instance and may improve site response. 

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

* Set FQDN on internet facing vm or container: 
  - edit `/etc/hosts` in vm or container using your favourite editor as root `$ sudo nano /etc/hosts` and add second line replacing `<your.domain.xyz>` and `<hostname>` with your own **domain name** and **hostname**:
```
127.0.0.1       localhost
127.0.1.1       <your.domain.xyz>   <hostname>

## The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

## FQDN for Nextcloud snap instance, vm or container behind reverse proxy with termination

Setting FQDN (**F**ully **Q**ualified **D**omain **N**ame) is **best practice** and is **optional** on self-hosted Nextcloud snap instances running on vm or container behind reverse proxy **with termination** and may improve site response. 

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

## FQDN for Nextcloud snap instance, vm or container behind pass through reverse proxy without termination 

Setting FQDN (**F**ully **Q**ualified **D**omain **N**ame) is **best practice** and is **optional** on self-hosted Nextcloud snap instances running on vm or container behind **pass through** reverse proxy **without** termination and may improve site response.  

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

* Set FQDN on instance behind **pass through** reverse proxy **without** termination: 
  - edit `/etc/hosts` in vm or container using your favourite editor as root `$ sudo nano /etc/hosts` and add second line replacing `<your.domain.xyz>` and `<hostname>` with your own **domain name** and **hostname**:
```
127.0.0.1       localhost
127.0.1.1       <your.domain.xyz>   <hostname>

## The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
## Trusted proxies in Nextcloud snap
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

>[!CAUTION]
> Be aware that these values must be IPv4 or IPv6 addresses or IPv4 or IPv6 ranges in CIDR notation see [reverse proxy documentation](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/reverse_proxy_configuration.html)

> [!TIP]
> ### Local DNS
> 
> Make your Nextcloud snap instance known to your local DNS or Pihole by adding the instance IP and hostname to `/etc/hosts` on local DNS. This may improve site response as well as local up/download in your local network.

----
> [!NOTE]
>
> Be aware of differences between reverse proxy configurations where;
> * the reverse proxy **manages** encryption and **handles termination** (see [instance behind reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap#fqdn-for-nextcloud-snap-instance-behind-reverse-proxy)) and ([letting reverse proxy handle encryption](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager))
> * the reverse proxy **passes through** encryption **without termination** (see [internet facing vm or container](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap#fqdn-for-nextcloud-snap-instance-on-internet-facing-vm-or-container)) and ([putting Nextcloud snap behind a reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy))
>
> Also be aware of port configuration;
> * see [HTTP and HTTPS port configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Port-configuration#http-and-https-port-configuration)

