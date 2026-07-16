---
date: '2026-07-16T21:01:53+02:00'
draft: false
title: 'Installation Requirements'
---
<p align="center" width="100%">
    <img width="15%" src="https://github.com/user-attachments/assets/e32a41fc-59ad-45db-8bcc-572363f8ab7b" alt="Nextcloud snap"> 
</p>
<h1 align="center">Nextcloud snap requirements</h1>

You have decided to use Nextcloud snap to set up Nextcloud as a safe home for your data, that's great!

Nextcloud snap is a community driven [snap installation](https://snapcraft.io/nextcloud) making [Nextcloud](https://nextcloud.com/) easy to install and simple to maintain. The ideal Nextcloud snap is an "install and forget" Nextcloud instance that works on most architectures and [updates itself](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-Nextcloud-snap-with-Snap#managing-automatic-updates) without requiring administrative skills. Combining Nextcloud with [snapd](https://ubuntu.com/core/services/guide/snaps-intro) makes it a perfect fit for IoT or scalable environments. Snapd is a secure and robust technology which the Nextcloud snap team has embraced. The team packages the latest stable upstream Nextcloud, adds some snap magic and releases that snap package upon testing fresh installs and automated updates, so that the Nextcloud snap community has peace of mind.

However, Nextcloud snap is opinionated. The following recommended components are included and not optional;

- [x] Nextcloud snap uses recommended Apache 
- [x] Nextcloud snap uses recommended MySQL 
- [x] Nextcloud snap uses recommended PHP 
- [x] Nextcloud snap uses recommended Redis 

----

Before getting started be aware of what you expect from your Nextcloud instance and what system requirements have to be met to fulfil your needs. There are various aspects you might consider;
- [x] number of users
- [x] storage & space requirements
- [x] power consumption & efficiency
- [x] network & connectivity
- [x] backup & redundancy
- [ ] etc. ...

Plan your setup. Do some research, read the [docs](https://github.com/nextcloud-snap/nextcloud-snap) and the [wiki](https://github.com/nextcloud-snap/nextcloud-snap/wiki).

## Hardware
Whether you plan to run Nextcloud snap on a bare-metal server, a virtual server, a virtual machine, in a container or on a desktop PC, a notebook PC, a mini PC or on a single board computer like Raspberry Pi, you'll be up and running in no time.

The folks at [Canonical](https://ubuntu.com/community/governance/canonical) have made it really easy to [install Ubuntu](https://ubuntu.com/download/server) on almost any hardware out there.

## Operating system
Since [snaps](https://ubuntu.com/core/services/guide/snaps-intro) are Ubuntu-centric, its no surprise that Nextcloud snap was designed to run on Ubuntu and the reason why [Ubuntu is the only supported distribution](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Why-Ubuntu-is-the-only-supported-distro) for Nextcloud snap.

* [Ubuntu Server](https://ubuntu.com/download/server) for most hardware
* [Ubuntu IoT](https://ubuntu.com/download/iot) for Raspberry Pi

## Network
Assuming a network where the host running Nextcloud snap acquires a static local IPv4 address from DHCP/router and the required ports 80 and 443 are enabled and internet facing for that IPv4 address, the routers public IPv4 address must be available via [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) (**D**omain **N**ame **S**ystem) request.

- [x] static local IPv4 address from DHCP/router
- [x] internet facing Ports 80 and 443 for local IPv4 address 
- [X] DNS entry pointing to routers public IPv4 address

> [!NOTE]
> [**CG-NAT**](https://en.wikipedia.org/wiki/Carrier-grade_NAT) (**C**arrier **G**rade **N**etwork **A**ddress **T**ranslation) commonly known as [**DSLite**](https://en.wikipedia.org/wiki/Dual-Stack_Lite) (**D**ual **S**tack **L**ite) will not make a public IPv4 address available! See also [detecting CG-NAT](https://help.nextcloud.com/t/what-cg-nat-is-how-to-detect-it-why-it-is-bad-what-can-you-do-about-it/201908). There are workarounds however;
>
> + Contact your *service provider* and request a public IPv4 address (comp. **F**ull **D**ual **S**tack) 
> + Do some research about alternative [IPv6 to IPv4](https://en.wikipedia.org/wiki/6in4) tunnels/routing 
> + Examples of tunnelbroker providers [ROUTE64](https://www.route64.org) or [Cloudflare tunnel](https://blog.cloudflare.com/tunnel-for-everyone/)

## Domain name and DNS
While some folks own a domain name like `mydomain.com` connected to their static IPv4 address and will probably have a subdomain pointing to the host like `cloud.mydomain.com`. 

Self-hosting users will have a dynamic IPv4 address which could change under certain circumstances. These users require a [DDNS provider](https://en.wikipedia.org/wiki/Dynamic_DNS) (**D**ynamic**DNS** provider) to keep track of their routers public IPv4 address for connecting a *domain name* like `cloud.mydomain.mydnsprovider.xyz` pointing to their routers (dynamic) public IPv4 address.

There are plenty [DDNS providers](https://en.wikipedia.org/wiki/Dynamic_DNS) out there to choose from. Some come at a fee, some are free, many offer domain names, others don't. Often you will have a choice of domain names, sometimes you have to take what is available. Do some research and make the right choice for you.

Thus [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) (**D**omain **N**ame **S**ystem) resolves a “human readable domain name” to your public IPv4 address. That will be the domain name required by the client to reach your Nextcloud instance.

A DNS entry pointing to your public IPv4 address is a requirement for an SSL Certificate for [HTTPS encryption](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#https-encryption-with-lets-encrypt). 

----

Continue with [installation](https://github.com/nextcloud-snap/nextcloud-snap/wiki/install-Nextcloud-snap).
