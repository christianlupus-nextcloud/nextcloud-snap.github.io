---
date: '2026-07-16T21:01:56+02:00'
draft: false
title: 'Develop and Contribute'
---
# Nextcloud Snap Contribution Guide

Welcome! We're a pretty friendly community and we're thrilled that you want to
help make this snap even better. However, we do ask that you follow some general
guidelines while doing so, just so we can keep things organized around here.

1. Make sure an [issue][1] is created for the bug you're about to fix, or
   feature you're about to add. Keep them as small as possible.

2. We use a forking, feature-based workflow.

   Make a fork of this repository, and create a branch based on `develop` named
   specifically for the feature on which you'd like to work. Make your changes
   there. Commit often.

3. Squash commits into one, well-formatted commit. Mention the issue being
   resolved in the commit message on a line all by itself like `Fixes #<bug>`
   (refer to [closing issues via commit messages][2] for more keywords you can
   use).

   If you really feel like there should be more than one commit in your branch,
   then you're probably trying to introduce more than one feature and you should
   make another branch (and issue) for it.

4. Submit a pull request to get changes from your branch into `develop` (no
   merge requests should be made into `master`). Mention which bug is being
   resolved in the description.

[1]: https://github.com/kyrofa/nextcloud-snap/issues/new
[2]: https://help.github.com/articles/closing-issues-via-commit-messages/


# Develop and contribute

* [Hacking Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Hacking-Nextcloud-snap)
* [Included CLI utilities](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Included-CLI-utilities)
* [Developer FAQ's](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Developer-FAQ)
* [Nextcloud snap confinement](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Nextcloud-snap_Snap-confinement)

## Installing Nextcloud snap in writable mode

The snap is by definition read-only, but some times it's useful to poke at the contents of it to see what's going on. If we want to do that, there are some hoops we can jump through to get there. **Note that this is not recommended for production use.** The snap won't be as secure and will no longer update if you do this. This is handy for development/experimentation, nothing more.

I'm runnning these commands on a fully up-to-date Ubuntu 18.04, they are untested elsewhere. I know from experience that this does not work with home disk encryption (ecryptfs), but full disk encryption is fine.

### Download the snap in question

This will download both the snap and the store assertion (store signature for the snap). You can optionally specify the channel, it will default to using stable:

    snap download nextcloud [--channel=14/candidate]

### Unpack the snap

We're doing this so that we can modify it (otherwise we wouldn't be able to write to the squashfs image):

    unsquashfs nextcloud_10079.snap

That unpacks by default to a directory named "squashfs-root".

### Install the snap in read/write mode

We can use `snap try` to install the snap in read/write mode now that we've unpacked it:

    sudo snap try squashfs-root

### Use nextcloud and tinker away

The snap will fire up after the `snap try` command and start listening on port 80, so you can just visit `http://localhost` in your browser to create an admin user and get up and running. The Nextcloud sources are in `squashfs-root/htdocs/`, although note that the config being used is not in there, but in `/var/snap/nextcloud/current/nextcloud/config/`.

### Remove nextcloud when done

When you're satisfied with your tinkering, you can uninstall the Nextcloud snap like normal, getting rid of the config and all data:

    sudo snap remove nextcloud

# Pull requests

:warning: **Anyone can create a pull request**. Make sure you trust the author and/or the changes made by the author before installing a snap automatically built from the pull request. :warning:

You want to help evaluate a pull request? That's awesome, we'd love your help!

Whenever someone creates (or pushes an update to) a pull request, a snap is automatically built and released into an ephemeral channel unique to that pull request. You can see that happening as part of the status checks for that pull request. **Note: This is done for amd64 only**. If you would like to test another architecture that's excellent, it's just not automated-- please simply ask in the PR and one will be built for you.

[[images/pr-snap-builder.png]]

# Testing

## Testing pull requests

You can test the changes you see in the pull request by installing the snap from that channel:

    sudo snap install nextcloud --channel=beta/pr-<number>
or
    
    sudo snap install nextcloud --channel=latest/beta

----
## Testing or fresh install Nextcloud snap

### Setup testing environment per script (optional)

Create your testing environment on a dedicated machine, VM or container. Example LXD/LXC [reference and specs](https://github.com/scubamuc/scubamuc.github.io). You can also test on your productive system... but things could break.

You may want to automate installation for testing, thus maintaining a reproducible environment. No need to do repetitive stuff if you can automate it. See also [Nextcloud snap auto install example](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Develop-and-contribute#example-script) for quick automated fresh install tests on a dedicated machine, VM or container using an auto install script.

The method below enables quick PR testing (_10-15 minutes per test, depending on resources_). It is important to follow exactly the same procedure for every test so that any discrepancies are discovered immediately. Even the smallest deviations could have an impact. The test result should be identical each time it is run. Keep an eye on [upstream issues](https://help.nextcloud.com/latest), no need for redundancy 🤔

Set up a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager) pointing to your test domain `testcloud.mydomain.tld` and get an SSL certificate for that test domain.

#### Procedure

Assuming you're running your tests behind a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager)

* [Nextcloud-Info-overview](https://github.com/scubamuc/bash-scripts/blob/scubamuc-wiki/ncinfo_en.sh) script (optional)
* install Nextcloud snap latest/beta or PR `--channel=beta/pr-<number>` on testing host.
* complete Nextcloud [first start](https://github.com/nextcloud-snap/nextcloud-snap/wiki/install-Nextcloud-snap#first-start). this should be done manually to ensure "upstream" issues are resolved.
* run automated configuration script (see example below).
* truncate logs `sudo truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log` clearing first setup logs, restart `sudo snap restart nextcloud`
* check logs or run the [debugging script](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Issue-Debugging-Scripts#debugging-script).

#### Example script:

> [!IMPORTANT]
> :warning: **never copy and paste!** without reading the script first :warning:
>
> **read the script** :eyes: *edit* and *replace* `<variables>` & values :exclamation:


```bash
 #!/bin/bash
#################################################################################
                ## -scubamuc- https://scubamuc.github.io/ ##
## Setup Nextcloud snap testing environment --> LXC, bare-metal, VM etc...
## Script assumes your test instance is behind a reverse proxy managing SSL
## IMPORTANT: Script needs to be edited for your values --value=<yourvalue>
#################################################################################
## Optional auto setup admin user excluding recommended apps ##
#   sudo nextcloud.manual-install <ADMINUSER> <PASSWORD>
#################################################################################
### connect snap interfaces ###
# snap connect external media
        sudo snap connect nextcloud:removable-media
# snap connect network observe
        sudo snap connect nextcloud:network-observe
# snap enable HTTP compression
        sudo snap set nextcloud http.compression=true
### Nextcloud setup & configuration ###
## backup working config.php ##
    sudo cp /var/snap/nextcloud/current/nextcloud/config/config.php /var/snap/nextcloud/current/ ;
## set default phone region edit <GB, DE, IT>##
    sudo nextcloud.occ config:system:set default_phone_region --value="<GB>" ; 
## set trusted proxies (if reverse proxy is present) edit <REVERSE.PROXY.IP> ##
    sudo nextcloud.occ config:system:set trusted_proxies 0 --value="<REVERSE.PROXY.IP>" ;
## set trusted domains edit <CLOUD.MY.DOMAIN.TLD>##
    sudo nextcloud.occ config:system:set trusted_domains 0 --value="<CLOUD.MY.DOMAIN.TLD>" ;
## set overwritehostprotocol (optional) ##
    sudo nextcloud.occ config:system:set overwriteprotocol --value="https" ;
## set overwritehost (optional)
    sudo nextcloud.occ config:system:set overwritehost --value="<CLOUD.MY.DOMAIN.TLD>"
## update database
    sudo nextcloud.occ maintenance:mimetype:update-db ;
## fix missing indices
    sudo nextcloud.occ db:add-missing-indices ;
## fix missing mimetypes
    sudo nextcloud.occ maintenance:repair --include-expensive ;
## set mail address in user profile for admin user edit mail details##
    sudo nextcloud.occ occ user:setting <ADMINUSER> settings email "<ADMINUSER@EXAMPLE.TLD>"
## set email for admin user (optional) edit user mail values##
    sudo nextcloud.occ config:system:set mail_from_address --value="cloud.domain" ;
    sudo nextcloud.occ config:system:set mail_smtpmode --value="smtp" ;
    sudo nextcloud.occ config:system:set mail_sendmailmode --value="smtp" ;
    sudo nextcloud.occ config:system:set mail_domain --value="your.domain.tld" ;
    sudo nextcloud.occ config:system:set mail_smtpauth --value="1" ;
    sudo nextcloud.occ config:system:set mail_smtphost --value="your.smpt.host" ;
    sudo nextcloud.occ config:system:set mail_smtpport --value="port" ;
    sudo nextcloud.occ config:system:set mail_smtpname --value="smtpusername" ;
    sudo nextcloud.occ config:system:set mail_smtppassword --value="smtppassword" ;
    sudo nextcloud.occ config:system:set mail_smtpsecure --value="ssl" ;
### optional apps ### 
## enable twofactor_email
    sudo nextcloud.occ app:enable twofactor_email ;
## enable twofactor_totp
    sudo nextcloud.occ app:enable twofactor_totp ;
## enable notify_push
    sudo nextcloud.occ app:enable notify_push ;
## disable appapi
    sudo nextcloud.occ app:disable app_api ;


#################################################################################
## This section is optional for recommended apps and may be completely omitted!##
## auto setup Collabora built-in CODE & Nextcloud office for Nextcloud snap    ##
#################################################################################
## install required packages on host ##
	sudo apt install fontconfig glibc-source ;
## Set overwrite-cli edit <HTTPS://CLOUD.MY.DOMAIN.TLD>> ##
	sudo nextcloud.occ config:system:set overwrite.cli.url --value="<HTTPS://CLOUD.MY.DOMAIN.TLD>" ;
## 1. Disable CODE ##
	sudo nextcloud.occ app:disable richdocumentscode ;
## 2. Disable Nextcloud office ##
	sudo nextcloud.occ app:disable richdocuments ;
## 3. Delete public_wopi configuration ##
	sudo nextcloud.occ config:app:delete richdocuments public_wopi_url ;
## 4. Delete wopi_url configuration ##
	sudo nextcloud.occ config:app:delete richdocuments wopi_url ;
## 5. Enable Nextcloud office ##
	sudo nextcloud.occ app:enable richdocuments ;
## 6. Enable CODE ##
	sudo nextcloud.occ app:enable richdocumentscode ;

exit
```

#### Example test result
![grafik](https://github.com/user-attachments/assets/5ba2d439-0c83-4215-8927-fe6c1cfe40d1)

## Testing on existing installs

It's **not recommended** to test half-baked pull requests on productive instances. Although snap is robust and the revert process stable, don't swap channels on a productive instance. Prefer to stay on stable channel. 

* Managing Nextcloud snap [managing Nextcloud snap with snap](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-Nextcloud-snap-with-Snap)

* Backup or migrate your productive instance see [Backup, Restore & Migrate](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Backup-and-Restore)

If you wish to disregard this warning, you can update an existing instance with:

    sudo snap refresh nextcloud --channel=latest/beta/pr-<number>

Make sure to revert back to where you were once you've completed your testing:

    sudo snap revert nextcloud

----
## Testing on a dedicated machine, VM or container
 
For quick (5 to 10 min.) automated fresh install tests;
* fire up a dedicated machine, VM or container, 
* set up a [reverse proxy](https://github.com/nextcloud-snap/nextcloud-snap/wiki/NGINX-proxy-manager) pointing to your test domain `testcloud.mydomain.tld`, 
* get an SSL certificate for that test domain,
* run an auto install script [see example above](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Develop-and-contribute#example-script)  

----
## Testing or installing Nextcloud snap on VPS

* fire up your VPS and make sure snapd is installed, or run `# apt install snapd`
* issue command `# nextcloud.manual-install <ADMINUSER> <PASSWORD>` and continue manually **or**
  * copy the bash script to your VPS and make executable `# chmod +x <SKRIPTNAME>`
  * edit and replace `<VARIABLES>`, `<ADMINUSER>` and `<PASSWORD>` in the script
  * execute the script
* complete [HTTPS Lets Encrypt certification](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#https-encryption-with-lets-encrypt) `# nextcloud.enable-https lets-encrypt`
* set [trusted domain/domains](https://github.com/nextcloud-snap/nextcloud-snap/wiki/configure-Nextcloud-snap#trusted-domains-configuration) `# nextcloud.occ config:system:set trusted_domains 0 --value="<CLOUD.MY.DOMAIN.TLD>"`
* truncate logs and restart the snap `# truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log && snap restart nextcloud`
* open `<CLOUD.MY.DOMAIN.TLD>` in browser, install default apps and enjoy Nextcloud
* done :heavy_check_mark: 

#### Example script: 

> [!IMPORTANT]
> :warning: **never copy and paste!** without reading the script first :warning:
>
> **read the script** :eyes: *edit* and *replace* `<variables>` & values :exclamation:

```
 #!/bin/bash
#################################################################################
                ## -scubamuc- https://scubamuc.github.io/ ##
## Setup Nextcloud snap testing environment --> VPS                            
## Script assumes instance is a VPS with port 80 and 443 internet facing
## Script assumes you are root on VPS, no sudo required!  
## IMPORTANT: Script needs to be edited for your values --value=<yourvalue>
#################################################################################
## Auto-setup admin user **excluding** recommended apps                            
## IMPORTANT --> enter your adminuser and a SECURE password                    
## replace <ADMINUSER> and <PASSWORD> -- your adminuser and a SECURE password  
#################################################################################
    nextcloud.manual-install <ADMINUSER> <PASSWORD> 
## backup working config.php ##
    cp /var/snap/nextcloud/current/nextcloud/config/config.php /var/snap/nextcloud/current/ ;
## set default phone region edit <GB, DE, IT>##
    nextcloud.occ config:system:set default_phone_region --value="<GB>" ; 
## set http compression (optional) ##
    snap set nextcloud http.compression=true ;
## set trusted domains edit <CLOUD.MY.DOMAIN.TLD>##
    nextcloud.occ config:system:set trusted_domains 0 --value="<CLOUD.MY.DOMAIN.TLD>" ;
## set overwritehostprotocol ##
    nextcloud.occ config:system:set overwriteprotocol --value="https" ;
## disable appapi
    nextcloud.occ app:disable app_api ;
## update database
    nextcloud.occ maintenance:mimetype:update-db ;
## fix missing indices
    nextcloud.occ db:add-missing-indices ;
## fix missing mimetypes
    nextcloud.occ maintenance:repair --include-expensive ;
## set mail address in user profile for admin user ##
    nextcloud.occ occ user:setting <ADMINUSER> settings email "<ADMINUSER>@example.tld>"
## recommend start Lets Encrypt certification using built in service, see Wiki 
    nextcloud.enable-https lets-encrypt ;
## recommend truncate logs and restart the snap see Wiki
    truncate -s 0 /var/snap/nextcloud/current/logs/nextcloud.log ; 
    snap restart nextcloud ;
## ensure Nextcloud snap is up and running with valid certificate.
#################################################################################
```