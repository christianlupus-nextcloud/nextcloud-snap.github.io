---
date: '2026-07-16T21:01:38+02:00'
draft: true
title: 'Testing Pull Requests'
---
:warning: **Anyone can create a pull request**. Make sure you trust the author and/or the changes made by the author before installing a snap automatically built from the pull request. :warning:

You want to help evaluate a pull request? That's awesome, we'd love your help!

Whenever someone creates (or pushes an update to) a pull request, a snap is automatically built and released into an ephemeral channel unique to that pull request. You can see that happening as part of the status checks for that pull request. **Note: This is done for amd64 only**. If you would like to test another architecture that's excellent, it's just not automated-- please simply ask in the PR and one will be built for you.

[[images/pr-snap-builder.png]]

## Fresh installs

You can test out the changes you see in the pull request by installing the snap from that channel:

    $ sudo snap install nextcloud --channel=beta/pr-<number>

Once you're done testing, simply remove the snap:

    $ sudo snap remove nextcloud

## Existing installs

It's **not recommended** to test out half-baked pull requests on running instances. If you wish to disregard this warning, you can update an existing instance with:

    $ sudo snap refresh nextcloud --channel=latest/beta/pr-<number>

Make sure to revert back to where you were once you've completed your testing:

    $ sudo snap revert nextcloud