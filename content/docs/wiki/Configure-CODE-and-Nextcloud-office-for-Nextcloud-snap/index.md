---
date: '2026-07-16T21:01:34+02:00'
draft: false
title: 'Configure CODE and Nextcloud Office for Nextcloud Snap'
---
<h1 align="center">Collabora CODE & Nextcloud Office configuration</h1>

CODE (**C**ollabora **O**ffice **D**evelopment **E**dition) is the development version of [Collabora online](https://www.collaboraonline.com/code/). Together with [Nextcloud office](https://apps.nextcloud.com/apps/richdocuments), its perfect for home use or small teams but not recommended for production environments. [Collabora Online - Built-in CODE Server](https://apps.nextcloud.com/apps/richdocumentscode) (`richdocumentscode`) is a third party app included in the default Nextcloud installation and may require individual configuration. 

The following configuration examples consider two scenarios:

- Nextcloud snap is an internet facing instance with automated (SSL) HTTPS encryption using Lets Encrypt without a reverse proxy.
- Nextcloud snap is behind a reverse proxy which handles (SSL) HTTPS encryption and redirects only http to the Nextcloud snap.

###  1. Install missing packages
Install missing packages on host: 
```
sudo apt install fontconfig glibc-source
```

###  2. Set Hostname and FQDN

Set host in `/etc/hosts` on host, see [**Hosts & FQDN**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)

**Example for internet facing instance with automated (SSL):**
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

**Example for instance behind reverse proxy with termination:**
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

###  3. Set overwrite-cli in Config-php

Set `overwrite.cli.url` in [config-php](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php) by adding the following line to your configuration: 
```
  'overwrite.cli.url' => 'https://your.domain.xyz',
```
or issue command:

    sudo nextcloud.occ config:system:set overwrite.cli.url --value="https://your.domain.xyz"

Be aware that the value `https://your.domain.xyz` above will be the only allowed domain for editing documents in Nextcloud office! Any other [trusted domains](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-trusted-domains) will not be accepted by Built-in CODE. So you may need to instruct your users.

### 4. Set trusted proxy in Config-php

Set [trusted proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php#setting-trusted-proxies) in [config-php](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-config.php) if Nextcloud snap is behind a **reverse proxy**. 
```
  'trusted_proxies' => 
    array (
      0 => 'your.proxy.ip',
     ),
```
or issue command:

    sudo nextcloud.occ config:system:set trusted_proxies 0 --value="your.proxy.ip" ;

>[!CAUTION]
> Be aware that these values must be IPv4 address or IPv4 ranges in CIDR notation see [reverse proxy documentation](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/reverse_proxy_configuration.html)

###  5. Configure CODE

**5a.** Configure CODE & Office for Nextcloud snap on **AMD64**: \
These steps (in sequence) are required to reconfigure/reset and configure CODE & Nextcloud office on Nextcloud snap.

  1. **disable CODE**: `sudo nextcloud.occ app:disable richdocumentscode`
  2. **disable Office**: `sudo nextcloud.occ app:disable richdocuments` 
  3. **delete configs**: `sudo nextcloud.occ config:app:delete richdocuments public_wopi_url`
  4. **delete configs**: `sudo nextcloud.occ config:app:delete richdocuments wopi_url`
  5. **enable Office**: `sudo nextcloud.occ app:enable richdocuments`
  6. **enable CODE**: `sudo nextcloud.occ app:enable richdocumentscode`

 **5b.** Configure CODE & Office for Nextcloud snap on **ARM64**: \
These steps (in sequence) are required to reconfigure/reset and configure CODE & Nextcloud office on Nextcloud snap.

   1. **disable CODE**: `sudo nextcloud.occ app:disable richdocumentscode_arm64`
   2. **disable Office**: `sudo nextcloud.occ app:disable richdocuments` 
   3. **delete configs**: `sudo nextcloud.occ config:app:delete richdocuments public_wopi_url`
   4. **delete configs**: `sudo nextcloud.occ config:app:delete richdocuments wopi_url`
   5. **enable Office**: `sudo nextcloud.occ app:enable richdocuments`
   6. **enable CODE**: `sudo nextcloud.occ app:enable richdocumentscode_arm64`

You should get a green check if you've followed the above sequence for your device. 

![grafik](https://github.com/nextcloud-snap/nextcloud-snap/assets/54933878/c89e641d-ec54-4081-8e2d-7841b3b86072)

----

## Troubleshooting CODE on Nextcloud snap

**Your documents are not loading?**

<img src="https://github.com/nextcloud-snap/nextcloud-snap/assets/54933878/8afc6365-07e8-4ad4-b81b-9fd4fa9de0c9" alt="Your image title" width="250"/>

- Loading CODE after fresh install, app upgrade or after server restart may **take a while**, so be patient. See [here](https://github.com/CollaboraOnline/richdocumentscode#implementation)
  - some folks suggest installing `libfuse2` on host `sudo apt install libfuse2`
- For network connection issues, double check steps **1** to **4**
- To reset CODE configuration, repeat steps **5a** or **5b** 1-6 in sequence depending on your device
- For **ARM64** devices: There is an actual upstream issue with ARM64 devices since v23.5.5. \
Keep tracking and help with providing your system and setup information [here](https://github.com/CollaboraOnline/richdocumentscode/issues/226)

----
See also:
* [**Hosts and FQDN**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hosts-and-FQDN-for-Nextcloud-snap)

* [**HTTP and HTTPS port configuration**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Port-configuration#http-and-https-port-configuration)

* [**Putting Nextcloud snap behind a reverse proxy**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Putting-the-snap-behind-a-reverse-proxy)

* [**Let reverse proxy handle encryption**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager)

----

> [!TIP]
> * [**How to configure collabora CODE for Nextcloud snap with docker**](https://github.com/nextcloud-snap/nextcloud-snap/wiki/How-to-configure-collabora-CODE-with-docker)
>
> * [**How to configure collabora CODE on a separate server for Nextcloud snap**](https://github.com/yannicklescure/wiki-md/blob/main/nextcloud/install-configure-collabora-online-nextcloud-snap-server.md)
