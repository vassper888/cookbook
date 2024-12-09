# GeoIP

```bash
sudo mkdir -p /var/www/moodledata/geoip
```

```bash
cd /var/www/moodledata/geoip
```

```bash
wget https://git.io/GeoLite2-City.mmdb
```

```bash
cd ..
```

```bash
sudo chmod -R 0777 geoip
```

```bash
sudo chown -R www-data:www-data geoip
```

```
/admin/settings.php?section=locationsettings geoip2file
```

