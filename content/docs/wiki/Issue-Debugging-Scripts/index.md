---
date: '2026-07-16T21:01:31+02:00'
draft: false
title: 'Issue Debugging Scripts'
---
# Debugging script

Run the following command (copy and paste the entire thing into your terminal and press enter). Paste the output into the issue/PR; it's pre-formatted to be a nice comment, so **only make it an attachment if it's too long to be a comment**.

```
/bin/sh <<"EOF"
print_info()
{
        echo "### Status"
        echo "\`\`\`"
        sudo systemctl status --no-pager "snap.nextcloud.$1.service"
        echo "\`\`\`"

        echo "### Journal"
        echo "\`\`\`"
        sudo journalctl --no-pager -n200 -u "snap.nextcloud.$1.service"
        echo "\`\`\`"
}

start_section()
{
        echo "<details>"
        echo "  <summary>$1</summary>"
        echo "  <p>"
        echo ""
}

end_section()
{
        echo "  </p>"
        echo "</details>"
        echo ""
}

start_section "Apache"
print_info "apache"
echo "### Error log"
echo "\`\`\`"
sudo tail -n 200 /var/snap/nextcloud/current/logs/apache_errors.log
echo "\`\`\`"
end_section

start_section "PHP"
print_info "php-fpm"
echo "### Error log"
echo "\`\`\`"
sudo tail -n 200 /var/snap/nextcloud/current/logs/php-fpm_errors.log
echo "\`\`\`"
end_section

start_section "MySQL"
print_info "mysql"
echo "### Error log"
echo "\`\`\`"
sudo tail -n 200 /var/snap/nextcloud/current/logs/mysql_errors.log
echo "\`\`\`"
end_section

start_section "Redis"
print_info "redis-server"
echo "### Error log"
echo "\`\`\`"
sudo tail -n 200 /var/snap/nextcloud/current/logs/redis.log
echo "\`\`\`"
end_section

start_section "Certificate Renewer"
print_info "renew-certs"
end_section

start_section "Logrotate"
print_info "logrotate"
end_section

start_section "Nextcloud Cron"
print_info "nextcloud-cron"
end_section

start_section "Nextcloud Fixer"
print_info "nextcloud-fixer"
end_section

start_section "Nextcloud configuration"
echo "\`\`\`"
sudo /snap/bin/nextcloud.occ config:list
echo "\`\`\`"
end_section

start_section "Nextcloud logs"
echo "\`\`\`"
sudo tail -n 200 /var/snap/nextcloud/current/logs/nextcloud.log
echo "\`\`\`"
end_section

start_section "Over-mounts"
echo "\`\`\`"
mount | grep nextcloud
echo "\`\`\`"
end_section

start_section "snap version"
echo "\`\`\`"
snap version
echo "\`\`\`"
end_section

start_section "nextcloud version"
echo "\`\`\`"
snap list nextcloud --all
echo "\`\`\`"
end_section

start_section "nextcloud interfaces"
echo "\`\`\`"
snap connections nextcloud
echo "\`\`\`"
end_section

start_section "snapd logs"
echo "### Status"
echo "\`\`\`"
sudo systemctl status --no-pager "snapd.service"
echo "\`\`\`"

echo "### Journal"
echo "\`\`\`"
sudo journalctl --no-pager -n200 -u "snapd.service"
echo "\`\`\`"
end_section
EOF
```

----
----

# Advanced binaries

## Redis

- `redis-cli`:
   - The Redis command line interface. To connect to the nextcloud snap redis instance run the following command:
     ```
     sudo /snap/nextcloud/current/bin/redis-cli -s /tmp/snap-private-tmp/snap.nextcloud/tmp/sockets/redis.sock
     ```

## Advanced maintenance

- [database, apps & files maintenance](https://github.com/nextcloud-snap/nextcloud-snap/wiki/database%E2%80%90apps%E2%80%90files%E2%80%90maintenance)