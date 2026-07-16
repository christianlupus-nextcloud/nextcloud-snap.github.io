---
date: '2026-07-16T21:02:01+02:00'
draft: true
title: 'Smoke Tests'
---
## Setup

First, install the snap:

    $ sudo snap install --candidate nextcloud

## Basics

### Is it running?

1. Open http://localhost in the browser.   
2. Check that the Nextcloud page to create an admin account is opened.


### Can you create an admin account?

1. Enter admin credentials.
2. You should see a popup mentioning the sync tools. Close it, and you should see the default file scaffold (user's guide, etc.).


### Create a group

 * Open the admin dropdown from the top-right corner.
 * Select the `Users` option.
   + The Users page is opened.
 * Click the `Add Group` button.
 * Enter the $GROUP_NAME.
 * Click the `+` button.
   + The group is added to the list.


### Create a user

 * On the users page, enter the $USERNAME and $PASSWORD.
 * Select the $GROUP_NAME from the group drop down.
 * Click the `Create` button.
   + The user is added to the list.


### Install an application

 * Open the main dropdown from the top-right corner.
 * Select the `Apps` option.
   + The Apps page is opened.
 * Click the `Not enabled` option.
 * Click the `Enable` button of the `Default encryption module`.
   + The text of the button is now `Disable`.


### Enable server-side encryption

 * Log out and log in again to initialize the keys.
 * Open the admin dropdown from the top-right corner.
 * Select the `Admin` option.
   + The Admin page is opened.
 * Go to the `Server-side encryption` section.
 * Click the `Enable encryption` button.
   + The option has a check and the defaylt encryption module options are shown.

### Send an email

The Nextcloud snap doesn't support using sendmail as Nextcloud has it hard-coded. You can only complete this test if you have an SMTP server you can use.

 * Open the admin dropdown from the top-right corner.
 * Select the `Personal` option.
   + The Personal page is opened.
 * Enter your $USER_EMAIL.
 * Open again the admin dropdown.
 * Select the `Admin` option.
   + The Admin page is opened.
 * Go to the Email server section.
 * Fill out your SMTP information
 * Enter the `From address`.
 * Click the `Send email` button.
 * Go to your email client and check the received email.


### Log in as a user

 * Log out and on the login page, enter the $USERNAME and $PASSWORD.
 * Click the button with right arrow.
   + The files page is opened, with a dialog to get applications to sync.
 * Close the dialog.


### Create a text file

 * On the files page, click the `+` button.
 * Click the `Text file` option.
 * Enter the $FILE_NAME.
   + A dialog is opened to edit the file.
 * Enter some text.
 * Wait for the file to be saved.
 * Close the dialog.
   + The file is added to the list.
 * Click the created file.
   + The dialog is open to edit the file. It contains the text written before.
 * Open a terminal to check that the file is encrypted:

    $ sudo cat /var/snap/nextcloud/common/nextcloud/data/testuser/files/test.txt
    
   + The contents displayed are encrypted.


### Create a folder

 * On the files page, click the `+` button.
 * Click the `Folder` option.
 * Enter the $FOLDER_NAME
 * Press enter.
   + The folder is added to the list.
 * Click the folder.
   + The folder contents are shown, it's empty.


### Upload a file

 * On the files page, click the `+` button.
 * Click the `Upload` option.
   + The file selection dialog is opened.
 * Select a file.
   + A progress bar is shown during the upload.
   + The file is added to the list.
 * Click the file and check that it's the same you uploaded.


### Delete a file

 * On the files page, click the `...` button next to a file.
 * Click the `Delete` option.
   + The file is removed from the list.
 * Click the `Deleted files` button on the bottom-left corner.
   + The deleted file appears in the list.


### Restore a file

  * On the deleted files page, click the `Restore` button next to a file.
    + The file is removed from the list.
  * Go to `All files`.
  * Navigate to the folder that used to contain the file.
    + The restored file appears in the list.


## HTTPS

There are three variants of this: using self-signed certificates, using custom (gotten from elsewhere) certificates, and using Let's Encrypt. Test as many as you can, but note that Let's Encrypt requires a service with a public IP, and ports 80 and 443 open with a domain name pointing to it.

Note that these tests should be run in a private browser window, each in its own window, or the browser will cache HTTPS being enabled, which makes it annoying to disable it to test something else.

### Self-signed

1. Enable HTTPS requesting self-signed certificates:

       $ sudo nextcloud.enable-https self-signed

2. Verify that HTTP redirects to HTTPS.
3. Verify that you get a certificate warning due to the certificate's self-signed nature.
4. Verify that HTTPS can be successfully disabled:

       $ sudo nextcloud.disable-https


### Custom

1. Generate your own self-signed certificate and key:

       $ openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes

2. Place the key and cert into some place accessible by the snap (e.g. `$SNAP_COMMON` or `$SNAP_DATA`):

       $ sudo mv *.pem /var/snap/nextcloud/common/

3. Enable HTTPS in the snap and tell it to use the cert/key you just created:

       $ cd /var/snap/nextcloud/common
       $ sudo nextcloud.enable-https custom cert.pem key.pem cert.pem

4. Verify that HTTP redirects to HTTPS.
5. Verify that you get a certificate warning due to the certificate's self-signed nature.
6. Verify that HTTPS can be successfully disabled:

       $ sudo nextcloud.disable-https


### Let's Encrypt

1. Enable HTTPS requesting certificates from Let's Encrypt (note that Let's Encrypt limits the number of real certificates you can obtain in a given week. If you've hit that limit, use the `-t` option to obtain a certificate from their staging server instead):

       $ sudo nextcloud.enable-https lets-encrypt

2. Verify that HTTP redirects to HTTPS.
3. Verify that you don't get a warning in the browser (unless you used `-t`).
4. Verify that HTTPS can be successfully disabled:

       $ sudo nextcloud.disable-https


## Test external storage with Samba

1. Add a [samba share via External Storage](https://docs.nextcloud.com/server/11/admin_manual/configuration_files/external_storage/smb.html).
2. Verify that files can be downloaded/uploaded from the Samba share.


## Change ports

By default the snap listens on port 80. If you enabled HTTPS, it also listens on port 443, and redirects HTTP traffic to HTTPS.

Start these tests with HTTPS disabled.


## Get current ports

1. Run `sudo snap get nextcloud ports`
2. You should see a JSON dump showing ports 80 and 443.


### Change HTTP port

1. Change the HTTP port to port 81:

       $ sudo snap set nextcloud ports.http=81

2. Visit http://localhost again, and verify that you get some sort of "unable to connect" error.
3. Visit http://localhost:81 and verify that you see Nextcloud.


### Change HTTPS port

This test should be run in a private browser window, or HTTPS settings will be cached.

1. First, enable HTTPS (doesn't matter how, but [self-signed](#self-signed) is easiest).
2. Verify that visiting http://localhost:81 redirects to https://localhost
3. Change the HTTPS port to port 444:

       $ sudo snap set nextcloud ports.https=444

4. Close/reopen the private browser window, and verify that visiting http://localhost:81 redirects to https://localhost:444, and you see Nextcloud.


## Change PHP memory limit

By default the snap uses the default 128MB for PHP's memory limit. That may not be enough if you find the gallery is exhausting it trying to create previews for your larger images.

Start these tests with the memory limit set to the default.


## Get current settings

1. Run `sudo snap get nextcloud php`
2. You should see a JSON dump showing a single setting, `memory-limit`, set to `128M`.


### Change memory limit

1. Change the memory limit to 256MB:

       $ sudo snap set nextcloud php.memory-limit=256M

2. Visit http://localhost again, and verify that everything still works correctly. Ideally you have an image or set of images whose previews could not be generated without this change. Verify that their previews are generated.


## Migration from current release

1. Uninstall the candidate Nextcloud and install the current release:

       $ sudo snap remove nextcloud
       $ sudo snap install nextcloud

2. Walk through the [basics](#Basics) again, to try and get the previous release looking somewhat used.
3. Update the snap to the candidate:

       $ sudo snap refresh --candidate nextcloud

4. Verify that you can still visit http://localhost and everything is still the way you made it in the previous version. Note that if you enabled any third-party apps, you may need to re-enabled them, but their data is still there (an upstream Nextcloud issue, to be resolved in v12).
5. Repeat steps 1-4, but enable HTTPS between steps 2 and 3. Verify that HTTPS is still working correctly after the refresh.