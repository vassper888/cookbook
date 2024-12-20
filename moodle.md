# MOODLE



## Install MOODLE

```
screen -S moodle-install
```

Check free disk space (for new dev moodle need 1-3GB or prod moodle need >=10GB)

```bash
df -BG /
```

Go to www dir root

```bash
cd /var/www
```

Make "moodledata" dir

```bash
sudo mkdir -p moodledata
sudo chown -R www-data:www-data moodledata
sudo chmod 0777 moodledata
```

Clone MOODLE

```bash
git clone https://github.com/moodle/moodle.git
cd moodle
git branch --track MOODLE_404_STABLE origin/MOODLE_404_STABLE
git checkout MOODLE_404_STABLE
cd ..
```

<pre class="language-bash"><code class="lang-bash">sudo chown -R www-data:www-data moodle
<strong>sudo find moodle -type d -exec chmod -R 0755 {} \;
</strong>sudo find moodle -type f -exec chmod -R 0644 {} \;
</code></pre>

```bash
ADMINUSER=admin.lms
ADMINPASS=$(openssl rand -base64 28)
ADMINEMAIL=admin@host.edu
SUPPORTEMAIL=admin@host.edu

LMSLANG=ru
WWWROOT=https://ed.host.edu
DATAROOT=/var/www/moodledata
FULLNAME='LMS MOODLE 4.4'
SHORTNAME='LMS'

DBTYPE=pgsql
DBHOST=localhost
DBPORT=5437
DBSOCKET=/var/run/postgresql
DBNAME=moodle
DBUSER=moodle
DBPASS=P123uA19zx1DF
```

<pre class="language-bash"><code class="lang-bash"><strong>cd moodle
</strong><strong>sudo -u www-data php admin/cli/install.php --agree-license \ 
</strong>    \ --non-interactive
    \ --fullname=$FULLNAME
    \ --shortname=$SHORTNAME
    \ --lang=$LMSLANG
    \ --wwwroot=$WWWROOT
    \ --supportemail=$SUPPORTEMAIL
    \ --adminemail=$ADMINEMAIL
    \ --adminuser=$ADMINUSER
    \ --adminpass=$ADMINPASS
    \ --dataroot=$DATAROOT
    \ --dbtype=$DBTYPE
    \ --dbhost=$DBHOST
    \ --dbport=$DBPORT
    \ --dbsocket=$DBSOCKET
    \ --dbname=$DBNAME
    \ --dbuser=$DBUSER
    \ --dbpass=$DBPASS
</code></pre>

Up security:

```bash
sudo -u www-data php admin/cli/cfg.php \
    --name=passwordsaltmain --set=$(openssl rand -base64 40)
```

Up performance:

```bash
sudo -u www-data php admin/cli/cfg.php --name=enable_read_only_sessions --set=true
```

```
nano config.php
```

Add `define('CONTEXT_CACHE_MAX_SIZE', 7500);` after `$CFG = new stdClass();`

Save config.php: Ctrl+X, Enter

Show admin password

```bash
echo "MOODLE Install Complite"
echo "Admin username: $ADMINUSER"
echo "Admin password: $ADMINPASS"
echo "Admin email: $ADMINEMAIL"
```

***

## MOODLE on Cron

```bash
EDITOR=nano crontab -e
```

```bash
* * * * * sudo -u www-data php /var/www/moodle/admin/cli/cron.php >/dev/null
```

***

## MOODLE Auto Deply

```bash
cd /var/www
```

```bash
nano moodle-deploy.sh
```

```bash
#!/bin/bash
cd /var/www/moodle
git pull
cd ..
chown www-data:www-data moodle
sudo -u www-data php moodle/admin/cli/upgrade.php --non-interactive
sudo -u www-data php moodle/admin/cli/purge_caches.php
```

Save moodle-deploy.sh: Ctrl+X, Enter

```bash
chmod +x moodle-deploy.sh
```

```bash
gedit ~/.bashrc
```

```bash
alias moodle-deploy='/var/www/moodle-deploy.sh'
```

Save .bashrc

```bash
EDITOR=nano crontab -e
```

```bash
1 1 * * 0 moodle-deploy >/var/log/moodle-deploy.log
```

Save: Ctrl+X, Enter

***

## XSendFile

<pre class="language-bash"><code class="lang-bash"><strong>nano config.php
</strong></code></pre>

Add after line `$CFG->dataroot = ...`:

<pre class="language-php"><code class="lang-php"><strong>$CFG->xsendfile = 'X-Accel-Redirect';
</strong>$CFG->xsendfilealiases = array(
    '/dataroot/' => $CFG->dataroot,
    '/cachedir/' => '/var/www/moodle/cache',
    '/localcachedir/' => '/var/local/cache',
    '/tempdir/'  => '/var/www/moodle/temp',
    '/filedir'   => '/var/www/moodle/filedir',
);
</code></pre>

Save config.php: Ctrl+X

***

## GIT on MOODLE + UPGRADE + Cleare code

```bash
screen -S moodle-up
```

```bash
cd /var/www
```

```bash
cp -r moodle moodle_bak
```

```bash
sudo -u www-data php moodle/admin/cli/maintenance.php --enable
```

```bash
cd moodle
sudo -u www-data php moodle/admin/cli/maintenance.php --enable
git init
git remote add origin https://github.com/moodle/moodle.git
git branch --track MOODLE_404_STABLE origin/MOODLE_404_STABLE
git checkout MOODLE_404_STABLE

```

```bash
rm -r moodle
```

```bash
git clone https://github.com/moodle/moodle.git
cd moodle
git branch --track MOODLE_404_STABLE origin/MOODLE_404_STABLE
git checkout MOODLE_404_STABLE
cd ..
```

```bash
cp moodle_bak/config.php moodle/config.php
```

```bash
rsync -av --progress moodle_bak/ moodle/
cd moodle
git status

```

```bash
sudo chown -R www-data:www-data moodle
sudo find moodle -type d -exec chmod -R 0755 {} \;
sudo find moodle -type f -exec chmod -R 0644 {} \;
```

```bash
sudo -u www-data php moodle/admin/cli/upgrade.php --non-interactive
```

```bash
sudo -u www-data php moodle/admin/cli/maintenance.php --disable
```









***

## Upgrade MOODLE

```
screen -S moodle-up
```





***

## Migrade MOODLE DB from MySQL/MariaDB to PostgreSQL

```
screen -S moodle-db-migrate
```



***

## Run individual cron task

***

## Cache total flush

***

## Install Plugin

***

## Upgrade Plugin

***

## Delete Plugin

***

## Get list plugins

***

## Maintaince mode

***

## MOODLE checks

***

## Travel MOODLE to new server

```
ssh root@<moodle-app-a>
```

```
screen -S moodle-travel-a
```

