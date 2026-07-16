---
date: '2026-07-16T21:01:56+02:00'
draft: true
title: 'User FAQ'
---
**Q: I can't connect to my Nextcloud Box any more. What can I do?**

A: Connect a monitor/TV/projector to the HDMI port and see what the Box is telling you.
As an alternative, try connecting the hard drive to another USB port to see if that helps.

----

**Q: How to switch Nextcloud Box on/off?**
There is no button :) Is there a way to switch Nextcloud on/off in a friendly way?

A: You need to [connect to the Box using SSH](https://github.com/nextcloud/nextcloud-snap/wiki/How-to-connect-to-the-Nextcloud-Box-with-SSH) and type the following:

```bash
$ sudo shutdown -h now
```
Unfortunately, you then don't know when the process has completed unless the device is connected to a monitor, so you'll have to wait a few minutes and then disconnect the power source.

----

**Q: Is there a "sleep mode"?**

A: The hard drive has several sleep modes and spins down automatically after a few hours, but as long as there is some activity, from logs per example, then it won't be able to go to sleep. 

----

**Q: Can I change the PHP or Apache configuration?**

A: Unfortunately, this is not possible at this time

----

**Q: How can I access other storage locations?**

A: This requires some advanced skills. You will need to do the following:

1. Use SSH to type the following command: `$ sudo snap connect nextcloud:removable-media ubuntu-core:removable-media`
2. Mount your storage location inside /media
3. Use the eternal storage app to mount that folder inside Nextcloud

----