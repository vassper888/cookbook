# MOODLE



## Install MOODLE

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
ADMINPASS=$(openssl rand -base64 28)
```

<pre class="language-bash"><code class="lang-bash"><strong>cd moodle
</strong><strong>sudo -u www-data php admin/cli/install.php --agree-license \ 
</strong>    \ --non-interactive
    \ --lang=ru
    \ --wwwroot=https://ed.host.edu
    \ --supportemail=admin@host.edu
    \ --adminemail=admin@host.edu
    \ --adminuser=admin
    \ --adminpass=$ADMINPASS
    \ --dataroot=/var/www/moodledata
    \ --dbtype=pgsql
    \ --dbhost=localhost
    \ --dbport=5437
    \ --dbsocket=/var/run/postgresql
    \ --dbname=&#x3C;db name>
    \ --dbuser=&#x3C;db user>
    \ --dbpass=&#x3C;db password>
    \ --fullname=LMS 4.4
    \ --shortname=LMS
</code></pre>

```bash
sudo -u www-data php admin/cli/cfg.php \
    --name=passwordsaltmain --set=$(openssl rand -base64 40)
```

```php
define('CONTEXT_CACHE_MAX_SIZE', 7500);
```

Show admin password

```bash
echo "MOODLE admin password: $ADMINPASS"
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





***

## Migrade MOODLE DB from MySQL/MariaDB to PostgreSQL



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

