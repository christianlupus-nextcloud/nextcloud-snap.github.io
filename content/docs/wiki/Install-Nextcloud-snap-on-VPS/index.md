---
date: '2026-07-16T21:02:06+02:00'
draft: false
title: 'Install Nextcloud Snap on VPS'
---
# Install Nextcloud snap on VPS

### Requirements
 * Minimal VPS --> 1vcore, 1GBvram, 10GBvstorage 
   * or better --> 2vcore, 4GMvram, 20GBvstorage
 * Domain/subdomain pointing to VPS IP

Fire up your VPS, SSH login as `root` and make sure snapd is installed, or run `apt install snapd`
  * Install Nextcloud snap in shell **including** admin user but **excluding** recommended apps                            
    * replace `<ADMINUSER>` and `<PASSWORD>` with your **Adminuser** and a **Secure** password  
```
nextcloud.manual-install <ADMINUSER> <PASSWORD>
```

  * backup working config.php 
```    
cp /var/snap/nextcloud/current/nextcloud/config/config.php /var/snap/nextcloud/current/ 
```

* set default phone region edit `<GB, DE, IT>`
```    
nextcloud.occ config:system:set default_phone_region --value="<GB>"
```

* set http compression (optional) 
```    
snap set nextcloud http.compression=true
```

* set trusted domain/s edit <CLOUD.MY.DOMAIN.TLD> iterating (1..2..3..) for multiple domains/subdomains
```    
nextcloud.occ config:system:set trusted_domains 0 --value="<CLOUD.MY.DOMAIN.TLD>"
```

* set overwritehostprotocol 
```
    nextcloud.occ config:system:set overwriteprotocol --value="https" 
```

* disable appapi
```
    nextcloud.occ app:disable app_api 
```

* update database
```
    nextcloud.occ maintenance:mimetype:update-db
```
* fix missing indices
```
    nextcloud.occ db:add-missing-indices
```
* fix missing mimetypes
```
    nextcloud.occ maintenance:repair --include-expensive
```
* set mail address in user profile for admin user
```
    nextcloud.occ occ user:setting <ADMINUSER> settings email "<ADMINUSER>@example.tld>"
```
* recommend start Lets Encrypt certification using built in service
```
    nextcloud.enable-https lets-encrypt 
```
* recommend truncate logs
```
    truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log
```
* restart Nextcloud snap
```
    snap restart nextcloud 
```

* open `<CLOUD.MY.DOMAIN.TLD>` in browser, install default apps and enjoy Nextcloud
* done :heavy_check_mark: 

This procedure may be skripted, see [example script](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Develop-and-contribute#example-script-1)