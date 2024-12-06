# MOODLE



### Install new MOODLE



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

```
ADMINPASS=$(openssl rand -base64 28)
```

<pre class="language-bash"><code class="lang-bash"><strong>cd moodle
</strong><strong>sudo -u www-data php admin/cli/install.php --agree-license \ 
</strong>    \ --non-interactive
    \ --lang=ru
    \ --wwwroot=https://ed.host.edu
    \ --adminemail=admin@host.edu
    \ --adminuser=admin
    \ --adminpass=$ADMINPASS
    \ --dataroot=/var/www/moodledata
    \ --dbtype=pgsql
    \ --dbname=&#x3C;db name>
    \ --dbuser=&#x3C;db user>
    \ --dbpass=&#x3C;db password>
    \ --fullname=LMS MOODLE 4.4
    \ --shortname=MOODLE 4.4
</code></pre>

Set MOODLE on cron

```bash
crontab -e
```

```bash
* * * * * sudo -u www-data php /var/www/moodle/admin/cli/cron.php >/dev/null
```

