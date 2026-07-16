---
date: '2026-07-16T21:01:32+02:00'
draft: false
title: 'Nextcloud Snap Security Brute Force Protection'
---
# Managing security brute force protection and throttling in Nextcloud snap

> Nextcloud has built-in protection against brute force attempts.

> The brute force protection feature is meant to protect Nextcloud servers from attempts to guess passwords and tokens in various ways. Besides the obvious “let’s try a big list of commonly used passwords” attack, it also makes it harder to use slightly more sophisticated attacks via the reset password page or trying to find app password tokens. It is used throughout the Nextcloud ecosystem, including by other apps, if they have sensitive entrypoints (and choose to enable support for it).

See https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/bruteforce_configuration.html

You may be testing something and are absolutely sure, that it is your IP being throttled and would like to reset the logged attempts,

List logged brute force attempts:
```
sudo nextcloud.occ security:bruteforce:attempts <your.external.ip.address>
```
this will display the number of brute force attempts from **your** IP-address
```
  - bypass-listed: false
  - attempts: 13
  - delay: 25000
``` 

Reset brute force attempts:
```
sudo nextcloud.occ security:bruteforce:reset <your.external.ip.address>
```
## Whitelisting
you may consider whitelisting an IP-range if you have a static IPv4 address

## Reverse proxies

> [Brute force protection and load balancers/reverse proxies](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/bruteforce_configuration.html#brute-force-protection-and-load-balancers-reverse-proxies)

> If you are behind a reverse proxy or load balancer it is important you make sure it is setup properly. Especially the trusted_proxies and forwarded_for_headers config.php variables need to be set correctly. Otherwise it can happen that Nextcloud actually starts throttling all traffic coming from the reverse proxy or load balancer. For more information see [Reverse proxy](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/reverse_proxy_configuration.html).

See 
* [Reverse proxy configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy)
* [PHP configuration options](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php)
* [Hosts & FQDN configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)



