---
date: '2026-07-16T21:01:23+02:00'
draft: false
title: 'Included CLI Utilities'
---
There are a few CLI utilities included:

- `nextcloud.occ`:
    - Nextcloud's `occ` configuration tool. You can always edit the config file
      directly (`/var/snap/nextcloud/current/nextcloud/config/config.php`) but
      the configuration tool provides a CLI interface for it. See
      `nextcloud.occ -h` for more information. Note that it requires `sudo`.
- `nextcloud.mysql-client`:
    - MySQL client preconfigured to communicate with Nextcloud MySQL server.
      This may be useful in case you need to migrate Nextcloud installations.
      Note that it requires `sudo`.
- `nextcloud.mysqldump`:
    - Dump Nextcloud database to stdout. You should probaby redirect its output
      to a file. Note that it requires `sudo`.
- `nextcloud.enable-https`:
    - Enable HTTPS via self-signed certificates, Let's Encrypt, or custom
      certificates. HTTP will redirect to HTTPS. Non-custom certificates will
      automatically be kept up-to-date. See `nextcloud.enable-https -h` for more
      information. Note that it requires `sudo`.
- `nextcloud.disable-https`:
    - Disable HTTPS (does not remove certificates). Note that it requires
      `sudo`.
- `nextcloud.manual-install`:
    - Manually install Nextcloud instead of visiting it in your browser. This
      allows you to create the admin user via the CLI. Note that it requires
      `sudo`.
- `nextcloud.export`:
    - Export data suitable for migrating servers. By default this includes the
      Nextcloud database, configuration, and data. See `nextcloud.export -h` for
      more information. Note that it requires `sudo`.
- `nextcloud.import`:
    - Import data exported from another Nextcloud snap instance (via
      `nextcloud.export`). By default this imports the database, config, and
      data. See `nextcloud.import -h` for more information. Note that it
      requires `sudo`.
