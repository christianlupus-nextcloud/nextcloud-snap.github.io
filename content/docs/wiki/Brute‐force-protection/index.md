---
date: '2026-07-16T21:02:07+02:00'
draft: true
title: 'Brute‐force Protection'
---
# Brute-force protection

Nextcloud features brute-force protection which will block an ip-address or disable a user after too many failed login attempts. 

See [Brute-force settings app](https://apps.nextcloud.com/apps/bruteforcesettings)

See [Nextcloud documentation](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/bruteforce_configuration.html)

## Unblock / reset an ip-address

List logged brute force attempts:
```
sudo nextcloud.occ security:bruteforce:attempts <your.external.ip.address>
```
this will display whether **your** IP-address is whitelisted, the number of brute force attempts from **your** IP-address and the throttling duration
```
  - bypass-listed: false
  - attempts: 13
  - delay: 25000
``` 

Reset brute force attempts:
```
sudo nextcloud.occ security:bruteforce:reset <your.external.ip.address>
```

or 

## Re-enable a disabled user  

issue command on host:
```
sudo nextcloud.occ user:enable <name of user>
```
