---
date: '2026-07-16T21:01:43+02:00'
draft: false
title: 'Configure HPB Client Push for Nextcloud Snap'
---
<h1 align="center">HPB client-push configuration</h1>

Developers GitHub: https://github.com/nextcloud/notify_push

> This app attempts to solve the issue where Nextcloud clients have to periodically check the server if any files have been changed. In order to keep sync snappy, clients wants to check for updates often, which increases the load on the server.
>
> With many clients all checking for updates a large portion of the server load can consist of just these update checks.
>
> By providing a way for the server to send update notifications to the clients, the need for the clients to make these checks can be greatly reduced.
>
> Update notifications are provided on a "best effort" basis, updates might happen without a notification being sent and a notification can be sent even if no update has actually happened. Clients are advised to still perform periodic checks for updates on their own, although these can be run on a much lower frequency.

> [!NOTE]
> HPB = **H**igh **P**erformance **B**ackend \
> The following configuration was tested on a fresh Ubuntu Server 22.04 LTS running on a x86_64 processor & on an Ubuntu Server 20.04 LTS running on an aarch64 processor. You need to adjust this steps depending on your OS.

# FQDN / trusted proxy configuration

Verify FDQN is correctly configured and trusted proxies are correctly set up.
See [Hosts & FQDN configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)

# Install

## A. Install the Nextcloud app
Install the "Client Push" app via the App Store or via the cli: \
`sudo nextcloud.occ app:enable notify_push`

## B. Create the systemd unit
1. Create and open a new systemd unit file via `sudo nano /etc/systemd/system/notify_push.service` and paste the following template in:

```ini
[Unit]
Description = Push daemon for Nextcloud clients
After = snap.nextcloud.nextcloud-fixer.service

[Service]
Environment=SOCKET_PATH=/tmp/snap-private-tmp/snap.nextcloud/tmp/sockets/notify_push.sock
Environment=DATABASE_URL=mysql://nextcloud:CHANGEME@localhost/nextcloud?socket=/tmp/snap-private-tmp/snap.nextcloud/tmp/sockets/mysql.sock
Environment=REDIS_URL=redis+unix:///tmp/snap-private-tmp/snap.nextcloud/tmp/sockets/redis.sock
ExecStartPre=/bin/bash -c 'while /bin/systemctl is-active --quiet snap.nextcloud.nextcloud-fixer.service; do sleep 10; done'
TimeoutStartSec=15m
ExecStart=/var/snap/nextcloud/current/nextcloud/extra-apps/notify_push/bin/ARCHITECTURE/notify_push /var/snap/nextcloud/current/nextcloud/config/config.php
Type=notify
User=root

[Install]
WantedBy = multi-user.target
```

2. Adjust the template with some system configurations:
  - Replace `ARCHITECTURE` with one of the following, depending on your system. Some examples:
    - x86_64
    - aarch64
    - armv7
  - Replace `CHANGEME` in `DATABASE_URL` with the config value `dbpassword`. \
  Get via cli with: `sudo nextcloud.occ config:system:get dbpassword`.

## C. Start & enable the systemd unit
1. `sudo systemctl start notify_push`
2. `sudo systemctl enable notify_push`

## D. Enable the reverse proxy
Enable the reverse proxy configuration for notify_push using the following command: \
`sudo snap set nextcloud http.notify-push-reverse-proxy=true`

## E. Setup the Nextcloud app
Setup the app via the following command: \
`sudo nextcloud.occ notify_push:setup https://cloud.example.com/push` \
Replacing `cloud.example.com` with your instance domain.

# Verify client push operation
## Verify notify_push metrics using `occ` commands
View client push metrics:
```
sudo nextcloud.occ notify_push:metrics
```
example output
```
Active connection count: 1
Active user count: 1
Total connection count: 10
Total database query count: 2
Events received: 12
Messages sent: 0
Messages sent (file): 0
Messages sent (notification): 0
Messages sent (activity): 0
Messages sent (custom): 0
```

## Notify push self test
testing notify push functionality:
```
sudo nextcloud.occ notify_push:self-test
```

result should be something like:
```
✓ redis is configured
✓ push server is receiving redis messages
✓ push server can load mount info from database
✓ push server can connect to the Nextcloud server
✓ push server is a trusted proxy
✓ push server is running the same version as the app
```


## Notify push logging and debugging
Debug option for notify_push
```
sudo nextcloud.occ notify_push:log debug
```
and
```
sudo journalctl -eu notify_push
```

By default, the push server only logs warnings, you can temporarily change the log level with an occ command
```
sudo nextcloud.occ notify_push:log <level>
```
Where level is error, warn, info, debug or trace, or restore the log level to the previous value using
```
sudo nextcloud.occ notify_push:log --restore
```


# Uninstall
## A. Stop & disable the systemd unit
1. `sudo systemctl stop notify_push`
2. `sudo systemctl disable notify_push`

## B. Uninstall the Nextcloud app
Uninstall the "Client Push" app via the App Store or via the cli: \
`sudo nextcloud.occ app:remove notify_push`

## C. Disable the Reverse Proxy
Disable the reverse proxy configuration for notify_push using the following command: \
`sudo snap set nextcloud http.notify-push-reverse-proxy=false`

# Troubleshooting

- Ensure you [configured `overwrite.cli.url`](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-CODE-and-Nextcloud-office-for-Nextcloud-snap#3-set-overwrite-cli-in-config-php) if required. Otherwise you may not be able to continue the setup.
- Ensure you added `127.0.0.1` to `trusted_proxies` in config.php:
```
'trusted_proxies' =>
array (
  0 => '127.0.0.1',
),
```
# Configuration when using Caddy as a reverse proxy
## Nextcloud

When using Caddy as a reverse proxy in front of you nextcloud instance, you also have to take this into account: the `trusted_proxies` array must contain all IP addresses in the chain that will forward connections to the server.

In the following example, where Caddy and the Nextcloud snap are both running as containers on an Incus server, and the Caddy container has two network interfaces:
1. one physical (`eth0`) connected to the home network (with range `192.168.0.1/24`),
2. and another one (`enp4s0`) to the bridge network (`incusbr0` with range `10.177.116.1/24`).

```
+------------------------------------------+----------------------------------+
|                   NAME                   |            IPV4                  |
+------------------------------------------+----------------------------------+
| caddy-container                          | 10.177.116.10 (eth0)             |
|                                          | 192.168.0.25 (enp4s0)            |
+------------------------------------------+----------------------------------+
| nextcloud-container                      | 10.177.116.11 (eth0)             |
+------------------------------------------+----------------------------------+
```

You would need to add the following to the `trusted_proxies` array:
```
$ sudo nextcloud.occ config:system:get trusted_proxies
127.0.0.1
10.177.116.10
192.168.0.25
<public-IP>
```

## Caddy
Besides that, the `<public-IP>` needs to be in the Caddy's [trusted_proxies](https://caddyserver.com/docs/caddyfile/options#trusted-proxies):

Following the example above, this should be added to the `Caddyfile`:
```
{
        servers {
                trusted_proxies static <public-IP>/32
        }
}
```

----

> [!TIP]
> Set Nextcloud Talk push notification in config.php to enable notification

## Setting Talk push notification

edit `config.php` and add/edit line in `config.php`
```
'has_internet_connection' => 'true',
```
or issue command on host:

    sudo nextcloud.occ config:system:set has_internet_connection --value="true"

