---
date: '2026-07-16T21:01:51+02:00'
draft: true
title: 'How to Configure Talk HPB With Docker'
---
# High Performance Backend for Talk in Nextcloud with Docker

## Please prefer the [Nextcloud community how-to](https://help.nextcloud.com/t/high-performance-backend-for-talk-on-nextcloud-with-docker/215828)

> [!NOTE]
> Be aware, this how to is **provided as-is** and is not supported by the Nextcloud snap team!

* [Nextcloud Talk documentation](https://nextcloud-talk.readthedocs.io/en/latest/quick-install/)

A **H**igh **P**erformance **B**ackend (HPB) requires a signalling service and consists of three components working hand in hand;

1.  **STUN** service (**S**ession **T**raversal **U**tilities for **N**AT)  for *discovering* NATed/firewalled external IP's. Nextcloud provides a default STUN server but can be replaced with your own.
    +  Examples: `stun.nextcloud.com:443` or `stun.yourdomain.tld:3478` or both
3.  **TURN** service (**T**raversal **U**sing **R**elays around **N**AT) like "coturn" or "eturnal" for *connecting* NATed/firewalled external IP's and controlling WebRTC streams. Its main role is to help WebRTC clients behind routers to communicate and relays the audio and video data through firewalls and port restrictions.
    + Examples: `turn.yourdomain.tld:3478` or `yourdomain.tld:3478`
    + Encryption is optional for TURN & STUN and will be handled by encrypted Nextcloud
5.  **WebRTC Signalling** service like "[Janus WebRTC server](https://janus.conf.meetecho.com/)" or "[Spreed WebRTC server](https://www.spreed.eu/contact-nextcloud-talk-high-performance-backend)" is required for calls and conversations with multiple participants. The signaling server is used to establish a WebSocket  connection between the participants in the call. Without it, all participants would have to upload their own audio/video individually for each other participant causing connectivity issues.
    + Example: `https://signal.yourdomain.tld`

Official documentation: 
  * [Nextcloud Talk install documentation](https://nextcloud-talk.readthedocs.io/en/latest/quick-install/)
  * [Nextcloud Talk TURN documentation](https://nextcloud-talk.readthedocs.io/en/latest/TURN/)

Thanks to snap user [sbe-arg](https://github.com/sbe-arg) and the folks at [Nextcloud AIO](https://github.com/nextcloud/all-in-one) and their fantastic work, this example setup uses their image which includes all the necessary parts (eturnal and janus) for the Talk:HPB but **excludes** recording. The dockerfile can be found in the [Nextcloud AIO repository](https://github.com/nextcloud/all-in-one/blob/main/Containers/talk/Dockerfile).

# Setup & configure Talk:HPB

This example will require [Docker](https://www.docker.com/) and a [Reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy) for the signalling server, forwarding and encrypting HTTP port 8181 to `https://mysignal.mydomain.tld`

## Prepare router/firewall & reverse proxy

* Allow inbound bypass for TURN & STUN on port 3478 tcp/udp which must be internet facing in router/firewall (i.e your.domain.tld:3478) 
   - encryption is optional for TURN & STUN as it will be handled by encrypted Nextcloud
* Set reverse proxy host for signal domain to forward and encrypt HTTP & WSS (Websockets Support) for port 8181 to `https://signal.yourdomain.tld`

> [!TIP] 
> ### Create a random 32 character password with `openssl` for each service;
> Make sure you create a long `secretpasswordkey` (min. 24 chars, better 32 chars) **for each service**! 
> Note down the `secretpasswordkeys` as you will need them for creating the Docker stack and for configuring Nextcloud talk.
> * issue command in host shell
>```bash
>openssl rand -hex 32
>```
>
> ![grafik](https://github.com/user-attachments/assets/ba52530d-ed98-4857-a224-fb969be28a8d)
>
>
>1. TURN_SECRET
>
> * issue command in host shell
>```bash
>openssl rand -hex 32
>```
>
>2. SIGNALING_SECRET  
>
> * issue command in host shell
>```bash
>openssl rand -hex 32
>```
>
>3.  INTERNAL_SECRET
>
> * issue command in host shell
>```bash
>openssl rand -hex 32
>```

## Create & run docker stack

```
name: 'hpb'

services:

  nc-talk:
    container_name: talk_hpb
    image: ghcr.io/nextcloud-releases/aio-talk:latest
    init: true
    ports:
      - 3478:3478/tcp
      - 3478:3478/udp
      - 8181:8081/tcp
    environment:
      - NC_DOMAIN=cloud.yourdomain.tld
      - TALK_HOST=mysignal.mydomain.tld
      - TURN_SECRET=78f11b6e0f6a6cabd4d402872fe... #this must be a long secretpasswordkey
      - SIGNALING_SECRET=d32a2258992a2abf3650cbdbceef... #this must be a long secretpasswordkey
      - TZ=Europe/Berlin
      - TALK_PORT=3478
      - INTERNAL_SECRET=8d26099c0669f62bf9aa80bff... #this must be a long secretpasswordkey
    restart: unless-stopped

```
## Configure Nextcloud talk

### Example Nextcloud STUN configuration,

![grafik](https://github.com/user-attachments/assets/1909f576-9e88-4d42-80ad-59ab858ede02)

### Example Nextcloud TURN configuration

![grafik](https://github.com/user-attachments/assets/abff3df2-a0fd-4a4f-aeae-ca6b4ad78cbe)

### Examples Nextcloud HPB configuration

Example 1
<img width="1136" height="382" alt="grafik" src="https://github.com/user-attachments/assets/0d3bd3b4-60da-4478-88fe-56e9f0345177" />

Example 2
![grafik](https://github.com/user-attachments/assets/c657a323-2b9b-48a7-8a5d-6ccb66fa8703)

----

### Example NGINX proxy manager:

![grafik](https://github.com/user-attachments/assets/b4f55ad6-bfe9-4fde-8a9a-68b0320a73cc)

----

### Example Apache reverse proxy configuration

for websocket upgrade (2.4.47 and later) in apache version

set
```
ProxyPass / http://192.168.2.15:8181/ upgrade=websocket
```

and:
```
RequestHeader set X-Forwarded-Proto expr=%{REQUEST_SCHEME}
RequestHeader set X-Real-IP expr=%{REMOTE_ADDR}
```
as mentioned [strukturag/nextcloud-spreed-signaling: Standalone signaling server for Nextcloud Talk.](https://github.com/strukturag/nextcloud-spreed-signaling#setup-of-frontend-webserver)

or [mod_proxy_wstunnel - Apache HTTP Server Version 2.4](https://httpd.apache.org/docs/2.4/mod/mod_proxy_wstunnel.html) and [mod_proxy - Apache HTTP Server Version 2.4](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#protoupgrade)

----

## Troubleshooting

### connection errors

`403 forbidden`

> [!IMPORTANT]
> make sure you have a long `secretpasswordkey` (**min. 24 chars**, better **32 chars**) for each service!

----

## NGINX proxy manager

#### Signaling (Talk backend)

* Hostname: signal.yourdomain.com
* Scheme/upstream: http → signaling:8080
* Options: enable Force SSL, HTTP/2, HSTS, WebSocket support

Advanced:
```
    # Allow larger payloads if needed
    client_max_body_size 100M;
    # Real‑time signaling works better without proxy buffering
    proxy_buffering off;
```
