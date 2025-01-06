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

Up security (ONLY FOR NEW MOODLE):

```bash
sudo -u www-data php admin/cli/cfg.php \
    --name=passwordsaltmain --set=$(openssl rand -base64 40)
```

Up performance:

ATTENTION!!! After installing enable\_read\_only\_sessions, you need to reset all authorization sessions.

<pre class="language-bash"><code class="lang-bash"><strong>sudo -u www-data php admin/cli/cfg.php --name=enable_read_only_sessions --set=true
</strong></code></pre>

```bash
sudo -u www-data php admin/cli/kill_all_sessions.php
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

## Cron

```bash
EDITOR=nano crontab -e
```

```bash
* * * * * sudo -u www-data php /var/www/moodle/admin/cli/cron.php >/dev/null
```

***

## Auto Deply

```bash
cd /var/www
```

```bash
nano moodle-deploy.sh
```

```bash
#!/bin/bash
cd /var/www/moodle
CHECKSTATUS=$(sudo -u www-data php admin/cli/checks.php --filter=environment,upgradecheck | head -c 2)
if [[ $CHECKSTATUS -noteq "OK" ]]; then
    echo "Bad check status."
    exit;
fi
sudo -u www-data php admin/cli/maintenance.php --enable
sudo -u www-data php admin/cli/cron.php --disable
git pull
git submodule foreach git fetch --all && git reset --hard && git pull && grunt amd
DATAROOT=$(php admin/cli/cfg.php --name=dataroot)
sudo chown -R www-data $DATAROOT
sudo chmod -R 0777 $DATAROOT
cd ..
chown www-data:www-data moodle
sudo find moodle -type d -exec chmod -R 0755 {} \;
sudo find moodle -type f -exec chmod -R 0644 {} \;
sudo -u www-data php admin/cli/uninstall_plugins.php --purge-missing --run
sudo -u www-data php admin/cli/upgrade.php --non-interactive
sudo -u www-data php admin/cli/purge_caches.php
sudo -u www-data php admin/cli/cron.php --enable
sudo -u www-data php admin/cli/maintenance.php --disable
sudo -u www-data php admin/cli/checks.php --filter=environment,upgradecheck
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

Hand run moodle-deploy:

```bash
moodle-deploy
```

Set moodle-deploy to run automatically every Sunday at 1 am:

```bash
EDITOR=nano crontab -e
```

```bash
1 1 * * 0 moodle-deploy >/var/log/moodle-deploy.log
```

Save: Ctrl+X, Enter

See moodle-deploy log:

```bash
less /var/log/moodle-deploy.log
```

or

```bash
lnav /var/log/moodle-deploy.log
```

***

## Backup 'moodledata'

```bash
cd /var/www
```

```bash
nano moodledata-backup.sh
```

```bash
#!/bin/bash

# check free space

rsync -avc /var/www/moodle/moodledata /mnt/moodle/backups/moodledata
```

Save moodledata-backup.sh: Ctrl+X, Enter

```bash
chmod +x moodledata-backup.sh
```

```bash
gedit ~/.bashrc
```

```bash
alias moodledata-backup='/var/www/moodledata-backup.sh'
```

Save .bashrc

Hand run moodledata-backup:

```bash
moodledata-backup
```

Setting up moodledata-backup to run automatically every day at 1am:

```bash
EDITOR=nano crontab -e
```

```bash
1 1 * * * moodledata-backup >/var/log/moodledata-backup.log
```

Save: Ctrl+X, Enter

See moodledata-backup log:

```bash
less /var/log/moodledata-backup.log
```

or

```bash
lnav /var/log/moodledata-backup.log
```

***

## Backup DB

```bash
cd /var/www
```

```bash
nano moodle-db-backup.sh
```

We make backups every day and store copies for the last 30 days.

```bash
#!/bin/bash

# check free space

BAKDIR=/mnt/moodle/backups/db
DAYS=30
CLIPATH='php /var/www/moodle/admin/cli/cfg.php'
DBTYPE=$($CLIPATH --name=dbtype --shell-arg --no-eol)
DBHOST=$($CLIPATH --name=dbhost --shell-arg --no-eol)
DBPORT=$($CLIPATH --name=dboptions['dbport'] --shell-arg --no-eol)
DBNAME=$($CLIPATH --name=dbname --shell-arg --no-eol)
DBUSER=$($CLIPATH --name=dbuser --shell-arg --no-eol)
DBPASS=$($CLIPATH --name=dbpass --shell-arg --no-eol)
export DBPASS

find $BAKDIR -name "*.sql.gz" -type f -mtime +$DAYS -delete

if [[ $DBTYPE -eq "pgsql" ]]; then
   pg_dump --host=$DBHOST --port=$DBPORT -U $DBUSER $DBNAME | gzip > $BAKDIR/pgsql_$(date "+%Y-%m-%d").sql.gz
elif [[ $DBTYPE -eq "mariadb" || $DBTYPE -eq "mysqli" ]]; then
   mysqldump --host=$DBHOST --port=$DBPORT -u $DBUSER -$DBPASS $DBNAME | gzip > $BAKDIR/mysql_$(date "+%Y-%m-%d").sql.gz
else
   echo "Unsupported database type: $DBTYPE"
fi

unset DBPASS
```

Save moodle-db-backup.sh: Ctrl+X, Enter

```bash
chmod +x moodle-db-backup.sh
```

```bash
gedit ~/.bashrc
```

```bash
alias moodle-db-backup='/var/www/moodle-db-backup.sh'
```

Save .bashrc

Hand run moodle-db-backup:

```bash
moodle-db-backup
```

Setting up moodle-db-backup to run automatically every day at 1am:

```bash
EDITOR=nano crontab -e
```

```bash
1 1 * * * moodle-db-backup >/var/log/moodle-db-backup.log
```

Save: Ctrl+X, Enter

See moodle-db-backup log:

```bash
less /var/log/moodle-db-backup.log
```

or

```bash
lnav /var/log/moodle-db-backup.log
```

Show backup list:

```bash
cd /mnt/moodle/backups/db && ls -l
```

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

Save config.php: Ctrl+X, Enter

***

## GIT + MOODLE + UPGRADE + Cleare code

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

## Migrade MOODLE DB: MySQL/MariaDB -> PostgreSQL

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

## Checks

***

## Travel MOODLE to new server

Step 1:

```
ssh root@<old-moodle-app>

screen -S moodle-travel

# check db size

# check free disk space for db dump

cd /var/www

sudo -u www-data php moodle/admin/cli/maintenance.php --enable

sudo -u www-data php moodle/admin/cli/kill_all_sessions.php

sudo -u www-data php moodle/admin/cli/purge_caches.php

EDITOR=nano crontab -e
# comment moodle cron and all backups cron

# make db dump

# check size moodledata

# check size moodle

# check size backups

# check size db dump

```

Step 2:

```
ssh root@<new-moodle-app>

screen -S moodle-travel

# check nginx and /var/www/moodle

# check free disk space

# check db type

# check db connect

# check php

# check /mnt backup dirs

# check /mnt free space

screen -S moodle-travel-moodledata
rsync <old-moodle-app>/moodledata/* /var/www/moodledata
sudo chown -R www-data:www-data /var/www/moodledata
sudo chmod -R 0777 /var/www/moodledata

screen -S moodle-travel-backups
rsync <old-moodle-app>/mnt/moodle/backups/* /mnt/moodle/backups
sudo chmod -R 0777 /mnt/moodle/backups

screen -S moodle-travel-moodle
rsync <old-moodle-app>/var/www/moodle/* /var/www/moodle
sudo chown -R www-data:www-data /var/www/moodle
sudo find /var/www/moodle-type d -exec chmod -R 0755 {} \;
sudo find /var/www/moodle-type f -exec chmod -R 0644 {} \;

screen -S moodle-travel-db
rsync <old-moodle-app>/var/www/moodle.sql.gz /var/www/moodle.sql.gz
gunzip < moodle.sql.gz | mysql -u moodle -p moodle
mysql -u moodle -p moodle
SHOW TABLES;
EXIT;
sudo rm moodle.sql.gz
```

Step 3: After upload 'moodledata', 'moodle' and 'db dump'

Set new IP app moodle server in A-records in DNS

And off maintenance mode:

```
ssh root@<new-moodle-app>

EDITOR=nano crontab -e
* * * * * sudo -u www-data php /var/www/moodle/admin/cli/cron.php >/dev/null

cd /var/www/moodle

sudo -u www-data php moodle/admin/cli/maintenance.php --disable
```

After update DNS A-records and maintenance mode check work moodle



After check work moodle set up backups on cron:

```
// Some code
```

Step 4: Optional (Removing data from the old server)

ATTENTION!!! Only after the complete transfer of data to the new server and making sure that the data on the new server is intact, everything works!

```
ssh root@<old-moodle-app>

screen -S moodle-remove

# remove moodle web-server host

# remove records in cron for moodle

sudo rm -r /var/www/moodle
sudo rm /var/www/moodle.sql.gz
sudo rm -r /var/www/moodledata
sudo rm -r /mnt/moodle/backups
sudo rm -r /var/log/moodle*.log
```
