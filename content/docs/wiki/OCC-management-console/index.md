---
date: '2026-07-16T21:01:39+02:00'
draft: false
title: 'OCC Management Console'
---
<h1 align="center">Nextcloud snap OCC management console</h1>


**OCC** (**O**wn **C**loud **C**onsole) is a commandline management interface requiring root privileges and is accessed from the Nextcloud snap host shell

![grafik](https://user-images.githubusercontent.com/54933878/224549354-c5609668-1ad1-4572-ac9a-14c681e612ad.png)

## Nextcloud management console usage

```
sudo nextcloud.occ
```
> [!TIP]
> Some systems don't add /snap/bin to the PATH. Try 
>
> ```sudo /snap/bin/nextcloud.occ```

### Nextcloud management console help
```
sudo nextcloud.occ --help
```

### View status
```
sudo nextcloud.occ status
```

### List console functions 
```
sudo nextcloud.occ list
```

### View system configuration
```
sudo nextcloud.occ config:list system
```

### Repair Nextcloud
```
sudo nextcloud.occ maintenance:repair
```

### View apps in Nextcloud
```
sudo nextcloud.occ app:list
```

### Enable apps
```
sudo nextcloud.occ app:enable <appname>
```

### Enable contacts birthday calendar
```
sudo nextcloud.occ dav:sync-birthday-calendar
```

### Disable apps
```
sudo nextcloud.occ app:disable <appname>
```

### Update apps
```
sudo nextcloud.occ app:update <appname>
```

### Update all apps
```
sudo nextcloud.occ app:update --all
```

### Update/enable unstable apps (development)
```
sudo nextcloud.occ app:update --allow-unstable <appname>
```

### List installed apps
```
sudo nextcloud.occ app:list
```

### Further app functions:
```
      app:disable
      app:enable
      app:getpath
      app:install
      app:list
      app:remove
      app:update
      config:app:delete
      config:app:get
      config:app:set
      files:scan-app-data
      files_external:applicable
      integrity:check-app
      integrity:sign-app
      user:add-app-password
```

### View all app and system configurations

```
sudo nextcloud.occ config:list all
```

### Further config functions:
```
      config:app:delete                    
      config:app:get                       
      config:app:set                       
      config:import                        
      config:list                          
      config:system:delete                 
      config:system:get                    
      config:system:set   
```

### Reset user password
```
$ nextcloud.occ user:resetpassword USERNAME
Enter a new password: 
Confirm the new password: 
Successfully reset password for USERNAME
```

### Reset admin password
```
sudo nextcloud.occ user:resetpassword admin
```

### Add user
```
sudo nextcloud.occ user:add USERNAME
```

### Disable two-factor authentication

This can be useful if the user forgot or lost their second factor. Afterwards users are free to enable this provider again via their personal settings.

list `<uid>` (= `user id`) two-factor authentication state:
```
sudo nextcloud.occ twofactorauth:state <uid>

``` 
disable two-factor authentication to enable reset
```
sudo nextcloud.occ twofactorauth:disable <uid> <provider_id>
```

## Nextcloud LDAP user management

### show LDAP remnant users
`sudo nextcloud.occ ldap:show-remnants`

### delete remnant LDAP users
``sudo nextcloud.occ user:delete <USERNAME>`

#### delete multiple remnant LDAP users

`sudo nextcloud.occ ldap:show-remnants | awk 'FNR > 3 {print $2}' | sed '$d' | xargs -L1 sudo nextcloud.occ user:delete `

