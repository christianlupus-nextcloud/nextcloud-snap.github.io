---
date: '2026-07-16T21:01:40+02:00'
draft: true
title: 'How to Connect to the Nextcloud Box With SSH'
---
Sometimes you need to log into the box' command line interface over a secure shell. This can enable you to install snaps or roll back changes or set up TLS (https) certificates.

The Nextcloud Boxes sold until April 2017 come with the RPI2 compatible image with the 'old' way of having a default ssh login/password combination. Since some time in April we will be shipping new RPI3 images (which can also be downloaded) that have a new, more secure connection mechanism.

# Old way

By default, the Box can be reached easily via your browser on the local network, see the inlay which came with your Box for the URL. But if you want to log in through ssh, follow the instructions below.

## 1. Find the Box

You can first try to use the hostname defined in the Box, which is:

`ubuntu-standard.local` or `ubuntu-standard.localdomain` or `nextcloud.local` (see the inlay which came with your Box)

If that doesn't work, you can open a command prompt in your favourite OS and type:

`$ arp -a`

In the list of entries, if you're using a Raspberry Pi 2, you should see one which starts with:

`b8-27`

Use the IP address next to it as the URL to use to connect to the Box

## 2. Connect to the Box

1. Use [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/) on Window or your Linux command line and open an SSH session using your Box's IP or internal hostname
1. Use the password/login ubuntu/ubuntu

## 3. Change your password

If you haven't done so already, you should change your SSH password

`$ passwd`

*Note: type your new password twice and make sure to remember it or you will lose access to the Box!*

# The RPI3 compatible new image
To enhance security, connecting your Nextcloud box now requires to create a user profile and a core system user on your Nextcloud box.

You have two options:
* either you plug a keyboard in the USB port and a monitor in the HDMI port to go through the Out-of-the-box-experience and complete the device configuration
* or you can create a secure system user on their unmanaged Core device. Then they can log into the device and, for example, install new snap applications.

In both cases, you need to create a Ubuntu SSO


## First option: Ubuntu SSO account for out-of-box experience (OOBE) 

This option requires to plug in a keyboard and a monitor to your Nextcloud box.

First, user needs to create a Ubuntu SSO Account.

1) Ubuntu SSO account is required to setup an user profile. User needs to create an Ubuntu SSO account before logging into Ubuntu Core 16. 
2) To create Ubuntu SSO account, go to ​https://login.ubuntu.com 
3) Complete account verification 

![](https://cloud.githubusercontent.com/assets/26001373/24001704/26c6b72c-0a5f-11e7-81d2-a11d5fdf236c.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24001705/26fd1bd2-0a5f-11e7-9149-880de3fbf191.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24001708/2702f836-0a5f-11e7-87d2-fe24f0d90f29.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24001706/26fee7e6-0a5f-11e7-91fa-0002aff896c4.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24001707/26ff9236-0a5f-11e7-8e48-3be264ebc1c7.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24001709/2726ac5e-0a5f-11e7-8750-bc6447ca4956.jpg)

5) Link to the instructions how to generate SSH Key: https://help.ubuntu.com/community/SSH/OpenSSH/Keys  
6) For Window users, please refer to the following link about how to generate SSH key: https://support.rackspace.com/how-to/generating-rsa-keys-with-ssh-puttygen 
7) User needs a keyboard and a monitor plugged to the device to be able go through OOBE and complete the device configuration. 
8) Internet connection is required.

## Out-Of-Box Experience (OOBE) 
1) At the beginning of OOBE, the device will prompt “Press enter to configure” 
![](https://cloud.githubusercontent.com/assets/26001373/24003556/15644198-0a64-11e7-9fb7-1779a4c92d59.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24003555/153f0630-0a64-11e7-879f-5f0d99ba0097.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24003557/15a7e150-0a64-11e7-8702-2636d06928b5.jpg)
![](https://cloud.githubusercontent.com/assets/26001373/24003775/aeac1e48-0a64-11e7-999e-9543c32e7f1d.jpg)

## Connect to NextCloud or the system 

After system boots up completely, the default hostname is set to “nextcloud”, and a MDNS service is also up, so if you would like to connect to Nextcloud in a local network: 
* Open a browser and use this URL: ​http://nextcloud.local​. 
* After a nextcloud user account is created, you can use ‘nextcloud.local’ to connect  to nextcloud from owncloud client and webdav 
 
After the OOBE is done completely, you can use SSH to log into the system: 
* $ ​ssh {default user}@nextcloud.local 
* Password: Ubuntu Core 16 uses SSH key of the default user to log in, so the private SSH key needs to be placed in the ~/.ssh folder. 

-------------------------------- 
## Second option: Creating a Core System User

This option allows you to connect to your Nextcloud box from your device.

You may want to create a secure system user on your unmanaged Core device. Then you can log into the device and, for example, install new snap applications. 

*Note​ that you can not add a system user to a device that already has one.*

An Ubuntu system is needed. If you do not have Ubuntu already you can create an Ubuntu virtual machine in Windows with VirtualBox, and do similar in Mac OS.

To make the system user with this procedure, you need the following:

* An Ubuntu 16.04 system. This is ​**step one**​ , covered below.
* An Ubuntu Store account. This is the first step of this documentation. In your account, you must fill
the ​ Developer namespace . ​ See: https://myapps.developer.ubuntu.com/dev/account/ ​ You can use firstnamelastname, or any other value.
* The ​ brand​ of the device. This is provided to you by the entity that created the Core system.
* The ​ model ​ of the device. This is also provided to you by the entity that created the Core system.

The result of this procedure is a new file (named “auto-import.assert”) that you copy to a USB stick and insert in your device. The device responds by creating your user account, so you can log in and, for example, install snap applications.

Note: ​ As mentioned, the user can only be created upon USB insertion if the device is not already ​ managed. This means the device cannot already have a system user. For example, some devices provide a configuration experience in an attached console when they turn on. If this configuration experience was ever completed and a user was created, then you cannot add another one using this procedure. Instead, you could log in using that
account.


### Step one: Get your Ubuntu 16.04 system

You need Ubuntu (desktop or server) 16.04. If you have Ubuntu 16.04 installed on a computer,you can skip this step and continue with creating the system user as explained below.

If you run Windows, you can create an Ubuntu system inside Windows using VirtualBox, and in Mac OS using similar steps. VirtualBox allows you to create ​ virtual machines inside your host operating system (inside Windows). Each virtual machine runs its own operating system inside a window in Windows. That is, the virtual machine has its own window and is just like any other application. The following link/document explains how to create a VirtualBox virtual machine in Windows and install Ubuntu16.04 into it. You can use this Ubuntu instance to complete the process of creating a system user file as described [here](https://docs.google.com/document/d/1ICArEXDFgYuWDIGYmDQX2BQDMx8c1ZOiX_EZRPXNi)

### Step two: Create your system user in Ubuntu Xenial

Here we cover how to use your Ubuntu 16.04 system to create a system user on your core device.

*Note:​ Screenshots in the following show Ubuntu running in a VirtualBox Ubuntu virtual machine on Windows. The commands and procedures shown here work in any Ubuntu 16.04 Desktop system, whether installed natively or in in a virtual machine.*

#### Launch the ​Terminal​ app

Click the top icon on the left panel (just beneath Ubuntu Desktop​ ) and type in “terminal”, then
click the ​ Terminal​ icon to launch it.
![terminal screenshot](https://cloud.githubusercontent.com/assets/26001373/24396308/066b2b46-13a3-11e7-9985-940c7a64af5d.png)

#### Install the ​ snapcraft​ package

You use snapcraft to login to the Ubuntu store and create and register a security key.

Enter the following command to install the best version of snapcraft and snapd:

`$ sudo apt update && sudo apt install snapcraft`

*Note​: The dollar sign (“​ $ ​ ”) in the command above represents the command prompt and is probably different in your terminal. You need to enter everything stated above after the dollar sign command prompt.*

Enter your password as prompted.

![](https://cloud.githubusercontent.com/assets/26001373/24396310/066da128-13a3-11e7-803c-8091207c4458.png)

#### Update ​snapd​ package

Please ensure snapd is up to date, as follows:

`$ sudo apt install snapd`

#### Sign in to your Ubuntu store account with ​ snapcraft

In the terminal, sign in with:

`$ snapcraft login`

Provide your Ubuntu store account email and password as prompted.

![](https://cloud.githubusercontent.com/assets/26001373/24396311/066e206c-13a3-11e7-85b3-2a562161cfcf.png)

#### Create a security key

Create a security key named “system-user” as follows (you can name it differently if you want,
but you need to use the name later):

`$ snapcraft create-key system-user`

Enter a password for this key as prompted.

*Note​: You ​ must remember this key’s password. You can not use the key without the
password.*

![](https://cloud.githubusercontent.com/assets/26001373/24396312/066ec800-13a3-11e7-8a73-57ec4cec3c1d.png)

It takes some time to create key. And it requires system activity to create it (this activity makes the key random and secure). During the period of creation, please do other activities in Ubuntu, for example, launch the Firefox browser (third icon from the top on the left in this case) and open tabs, open pages, and etc. The more you do, the faster the key is created.

The key is created when the terminal command prompt is returned, as here:

![Key created](https://cloud.githubusercontent.com/assets/26001373/24396307/066ab418-13a3-11e7-80b7-fceb8a42fe6d.png)

#### Register the security key

In the terminal, enter the following to register your new key:

`$ snapcraft register-key system-user`

Enter your Ubuntu store email and password as prompted.

And after that, enter your key password as prompted.

![](https://cloud.githubusercontent.com/assets/26001373/24396309/066b33f2-13a3-11e7-90d7-1710c4a752be.png)

#### Install the make-system-user snap

The make-system-user snap provides a tool that you can run to make a file (auto-import.assert) that you can later place on a USB stick and insert into a Core device to create the system-user account.

Install the make-system-user snap as follows:

`$ sudo snap install make-system-user --classic`

#### Create your system-user file

As noted with additional detail previously, you need the following from your device
provider:

* Brand: This can be a long set of letters and numbers). In this example we use “mybrand”.
* Model: This can be human readable or a not. In this example we use “mymodel”

*Note: The ​ brand ​ and ​ model ​ you use must be correct. They must be exactly what the device provider gave you (including capitalization). If they are not correct, the user will not be created on the system when you insert the USB stick into the device later (but the command here will succeed normally). This is a security feature that protects the system from having a user created by someone who does not know the required information. You also need the name of the snapcraft key you created previously, which in this case we named: “system-user”*

**Tip**:​ Display your keys with: ​ `$ snapcraft keys`

You also need a username and password for the account to be created on the device. In this case we use “myuser” and “mypassword”.

*Note:​​ You must not forget the username and password of the account.*

Run the following command, replacing the command arguments in ​ italics with the appropriate
values as explained above:

`$ make-system-user.run --brand ​ mybrand --model ​ mymodel --username myuser --password`

`mypassword --key ​ system-user`

Enter the password for your key as prompted:

On success , the following message displays:

> Done. You may copy auto-import.assert to a USB stick and insert it
> into an unmanaged Core system, after which you can log in using the
> username and password you provided.

And, the following file is created in your current directory: auto-import.assert

Tip : Display command help with `make-system-user.run --help`

You can verify the file exists with the ls command:

`$ ls`

`auto-import.assert`

#### Copy the auto-import.assert file onto a USB stick

**Tip**: ​If you are in a virtual machine on Windows, you may find it easiest to open your
web email (gmail, hotmail, etc.) and attach the file to a message you mail to yourself. Then,
in Windows, download the email attachment and copy it into the top level directory of a
USB stick.

### Insert the USB stick into the device

Wait 10 seconds or so to ensure the system-user is imported from the USB stick.

Log into the device with the user account

You can now log into the device in various ways. The approach you should take depends on the
particular device. One method is to sign in remotely from an Ubuntu system (or any system that
has the ssh command, for example (enter your new user’s password as prompted):

`$ ssh -o "IdentitiesOnly=yes" myuser@192.168.0.101 myuser@192.168.0.101's password:`

> Welcome to Ubuntu Core 16 (GNU/Linux 4.4.0-1030-raspi2 armv7l)
> * Documentation: https://help.ubuntu.com
> * Management: https://landscape.canonical.com
> * Support:
> https://ubuntu.com/advantage
> Welcome to Snappy Ubuntu Core, a transactionally updated Ubuntu.
> * See https://ubuntu.com/snappy
> It's a brave new world here in Snappy Ubuntu Core! This machine
> does not use apt-get or deb packages. Please see 'snap --help'
> for app installation and transactional updates.
> Last login: Fri Feb
> myuser@localhost:~$
> 3 15:55:37 2017 from 192.168.0.111
