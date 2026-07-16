---
date: '2026-07-16T21:02:09+02:00'
draft: false
title: 'Hacking Nextcloud Snap'
---
# Hacking Nextcloud snap

## Debug mode

By default, the snap installs itself in production mode, which prevents Apache
and PHP from providing any detailed version or library information in the HTTP
headers and error pages. Debug mode can be enabled with:

    $ sudo snap set nextcloud mode=debug

"debug" and "production" are the only valid modes.

## Hacking

If you change something in the snap, build it, install it, and you can run a
suite of acceptance tests against it. The tests are written in ruby, using
capybara and rspec. To run the tests, you first need to install a few
dependencies:

    $ sudo apt install gcc g++ make qt5-default libqt5webkit5-dev ruby-dev zlib1g-dev
    $ sudo gem install bundle
    $ cd tests/
    $ bundle install

Additionally, if you do not have X configured, install the following for a
'fake' X server.

    $ sudo apt install xvfb

Make sure the snap has a user called "admin" with password "admin" (used for
login tests):

