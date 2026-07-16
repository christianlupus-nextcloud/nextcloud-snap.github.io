---
date: '2026-07-16T21:01:25+02:00'
draft: true
title: 'Release Strategy'
---
This snap makes the following channels available:

| Channel | Description|
|---------|------------|
| latest/stable | The latest stable release of Nextcloud (**recommended**)<br />(Note that this may not be the latest version of Nextcloud available, but the latest version deemed stable enough for the snap's default channel.)|
| latest/candidate | Release candidates of the snap (not Nextcloud) |
| latest/beta | Trunk of development in the snap |
| latest/edge | Daily build of upstream Nextcloud's master branch<br />(what's coming in the next major release?) |
| 28/stable | The latest v28 stable release of Nextcloud |
| 28/edge | Daily build of upstream Nextcloud's stable28 branch<br />(what's coming in the next v28 release?) |
| 29/stable | The latest v29 stable release of Nextcloud |
| 29/edge | Daily build of upstream Nextcloud's stable29 branch<br />(what's coming in the next v29 release?) |

**Note that the only channels that receive significant testing and support are the ones that contain "stable" in their name.** The others are only here both for your convenience and so we can run tests against what's coming so we're made aware of any regressions coming from upstream.

You can install any of these with:

    $ sudo snap install nextcloud --channel=<channel>

Or, if you already have the snap installed, you can switch channels with:

    $ sudo snap refresh nextcloud --channel=<channel>