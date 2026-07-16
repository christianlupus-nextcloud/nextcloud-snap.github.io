---
date: '2026-07-16T21:01:51+02:00'
draft: true
title: 'Install Nextcloud Snap'
---
<p align="center" width="100%">
    <img width="15%" src="https://github.com/user-attachments/assets/e32a41fc-59ad-45db-8bcc-572363f8ab7b" alt="Nextcloud snap"> 
</p>
<h1 align="center">Nextcloud snap installation</h1>

[![Get it from the Snap Store](https://snapcraft.io/static/images/badges/en/snap-store-white.svg)](https://snapcraft.io/nextcloud)

Type the following in your terminal:
```
sudo snap install nextcloud
```
By default the latest stable Nextcloud snap release will be installed and it will automatically update to subsequent stable releases, but there are [other releases available as well](https://github.com/nextcloud/nextcloud-snap/wiki/Release-strategy) and you have full control of [automatic updates](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Managing-automatic-updates).

After installation, Nextcloud will start automatically.  Assuming you and the device on which it was installed are on the same network, you will reach the Nextcloud installation by visiting `<hostname>.local` or the IP address of the instance in your browser. If your hostname is `localhost`  or `localhost.localdomain`, like on an Ubuntu Core device, `nextcloud.local` will be used instead. 

## First start

Upon visiting the Nextcloud installation for the first time, you will be prompted to enter an admin _username_ and _password_ before Nextcloud is initialised. This may take a while depending on resources and the device.

<p align="center" width="100%">
    <img width="35%" alt="grafik" src="https://github.com/user-attachments/assets/b00ba1a3-0ce2-4590-9411-e0df59be1c1c" alt="first start">
</p>

After you provide that information you will be logged in and able to install apps, create users, and upload files.

<p align="center" width="100%">
    <img width="35%" alt="grafik" src="https://github.com/user-attachments/assets/94100335-27c2-438a-8894-57f65893a5db" alt="recommended appsstart">
</p>

See [configuration](https://github.com/nextcloud-snap/nextcloud-snap/wiki/Configure-Nextcloud-snap) and regain control over your data.
