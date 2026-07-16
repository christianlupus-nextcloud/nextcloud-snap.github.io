---
date: '2026-07-16T21:01:58+02:00'
draft: false
title: 'Release Management'
---
:construction: This page is still under construction. :construction:

We try to automate as much of our release management process as possible, but that's a journey, and there's still manual work needed. This document serves as a reference for understanding both what manual work is needed, as well as the entire automated system should anything need to be updated in the future.

# Systems involved

Our release management process relies heavily on the following systems:

- [**Launchpad**](https://launchpad.net/nextcloud-snap): The snap builder
  - A number of snap packages are setup in Launchpad to build if its underlying git branch changes
- **GitHub Actions**: Our CI system
  - Runs tests against each pull request
  - Syncs git changes to Launchpad, which spins up new snap builds depending on branch
  - Runs a number of scheduled jobs:
    - Daily: Modify `develop` branch such that it'll pull Nextcloud from a development branch, and push to Launchpad branches to spin daily builds
    - Daily: Run tests against the daily snaps, so we're aware of inbound breakage coming from upstream
- Snapcraft and the snap store: Where we manually promote/release versions of the snap built by Launchpad to the proper channels

# Development flow against develop branch

1. User makes pull request to `develop`
1. GitHub Actions runs various tests against it
1. Maintainers review it, give their +1
1. Maintainer merges it once two +1s are given
1. GitHub Actions syncs the merge commit from GitHub's `develop` branch over to [Launchpad's `develop` branch](https://code.launchpad.net/~nextcloud-snap-dev/nextcloud-snap/+git/nextcloud-snap/+ref/develop)
1. Launchpad, with a delay of up to 15 minutes, notices that change and triggers a build of [Launchpad's nextcloud-beta snap](https://launchpad.net/~nextcloud-snap-dev/+snap/nextcloud-beta).
1. Once that snap is complete, it is automatically released to the store into the `latest/beta` channel.

Nothing more is generally done with the snap once it reaches this point.

# Development flow against master branch

`master` is our release branch, so only maintainers make pull requests to it.

1. Maintainer makes pull request to `master`
1. GitHub Actions runs various tests against it
1. Maintainers review it, give their +1
1. Maintainer merges it once two +1s are given
1. Maintainer quickly creates an annotated tag of the merge commit
1. GitHub Actions syncs the merge commit (and tag) from GitHub's `master` branch over to [Launchpad's `master` branch](https://code.launchpad.net/~nextcloud-snap-dev/nextcloud-snap/+git/nextcloud-snap/+ref/master)
1. Launchpad, with a delay of up to 15 minutes, notices that change (and tag) and triggers a build of [Launchpad's nextcloud snap](https://launchpad.net/~nextcloud-snap-dev/+snap/nextcloud).
1. Once that snap is complete, it is automatically released to the store into the `latest/candidate` channel.
1. Maintainer merges master back into develop
1. GitHub Actions syncs the merge commit from GitHub's `develop` branch over to [Launchpad's `develop` branch](https://code.launchpad.net/~nextcloud-snap-dev/nextcloud-snap/+git/nextcloud-snap/+ref/develop)
1. Launchpad, with a delay of up to 15 minutes, notices that change and triggers a build of [Launchpad's nextcloud-beta snap](https://launchpad.net/~nextcloud-snap-dev/+snap/nextcloud-beta).
1. Once that snap is complete, it is automatically released to the store into the `latest/beta` channel, making it so `latest/beta` and `latest/candidate` may very well contain the same snap (unless more changes happened on develop in the meantime).

Once the snap reaches this point, it must be manually tested and verified. Once the maintainer is happy with it, it can be promoted to the stable channel:

    $ snapcraft promote nextcloud --from-channel=candidate --to-channel=stable

We'll also want to promote it to a version-specific channel as well. For example purposes, let's say it's version 27.0.1.snap1. In that case, we'd release to `27/stable`:

    $ snapcraft promote nextcloud --from-channel=candidate --to-channel=27/stable

Finally, now that it's released, we can close both `latest/candidate`:

    $ snapcraft close nextcloud candidate

Close `latest/beta` as well if it's containing the same version as `latest/candidate`.

# Development flow against version-specific branches

For this example, let's assume we want to make a change specifically to v26.

1. User makes pull request to `26`
1. GitHub Actions runs various tests against it
1. Maintainers review it, give their +1
1. Maintainer merges it once two +1s are given
1. If this is intended to be a release, the maintainer must quickly create an annotated tag of the merge commit
1. GitHub Actions syncs the merge commit (and tag, if any) from GitHub's `26` branch over to [Launchpad's `26` branch](https://code.launchpad.net/~nextcloud-snap-dev/nextcloud-snap/+git/nextcloud-snap/+ref/26)
1. Launchpad, with a delay of up to 15 minutes, notices that change (and tag, if any) and triggers a build of [Launchpad's nextcloud-26 snap](https://launchpad.net/~nextcloud-snap-dev/+snap/nextcloud-26).
1. Once that snap is complete, it is automatically released to the store into the `26/beta` channel.

If this wasn't intended to be a release, nothing more is done with the snap at this point. If it WAS intended to be a release, then like master it must be manually tested and verified. Once the maintainer is happy with it, it can be promoted to the `26/stable` channel:

    $ snapcraft promote nextcloud --from-channel=26/beta --to-channel=26/stable

**BE CAREFUL:** There are no guardrails preventing you from accidentally releasing this into `latest/stable`, or the `stable` branch of another major version. Check your typing. You might consider running `snapcraft status nextcloud` first, so you have a full revision map. Then, if you release to the wrong channel, you can fix it by taking a look at the revision map and re-releasing those proper revisions piecemeal, e.g.:

    $ snapcraft release nextcloud <old revision> latest/stable

Keep in mind that each architecture is its own revision, so you'll need to do this 5 times.