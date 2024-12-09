---
description: Install NGINX
---

# Nginx

## Install NGINX

```bash
sudo apt-get update -y && sudo apt-get upgrade -y
```

```bash
sudo apt install -y nginx
```

```bash
sudo systemctl start nginx
```

```bash
sudo systemctl enable nginx
```

```bash
sudo systemctl status nginx
```

***

## Set MOODLE host

```bash
sudo mkdir -p /var/www/moodle
```

```bash
sudo nano /etc/nginx/sites-available/moodle
```

<pre><code>server {
    server_name <a data-footnote-ref href="#user-content-fn-1">ed.host.edu</a>;
    root /var/www/moodle;
    index index.php index.html;
		
	large_client_header_buffers 5 64k;
	client_header_buffer_size 64k;
	client_max_body_size 3000M;
	client_body_buffer_size 128k;
	client_body_timeout 600s;

	send_timeout 15s;
	fastcgi_send_timeout 600s;
	fastcgi_read_timeout 600s;
	add_header X-Frame-Options "ALLOWALL-FROM ed.mgimo.ru";
	add_header Content-Security-Policy "frame-ancestors 'self' ed.mgimo.ru https://mgimo.proctoring.online";
	add_header Access-Control-Allow-Origin *;

	http2_max_field_size 64k;
	http2_max_header_size 64k;
		
        location /robots.txt {
                return 200 "User-agent: *\nDisallow: /\n";
        }
        location ~ /\.ht {
                deny all;
        }
        location ~ [^/]\.php(/|$) {
              add_header Access-Control-Allow-Origin *;
              include snippets/fastcgi-php.conf;
              fastcgi_pass unix:/run/php/php7.4-fpm.sock;
			  fastcgi_send_timeout 300;
			  fastcgi_read_timeout 600;
        }
        location ~ [^/]\.html(/|$) {
              add_header Access-Control-Allow-Origin *;
              add_header X-Frame-Options sameorigin;
        }
        location ~ [^/]\.htm(/|$) {
              add_header Access-Control-Allow-Origin *;
              add_header X-Frame-Options sameorigin;
        }
}
</code></pre>

```
nginx -t
```

```
sudo systemctl restart nginx
```

```
sudo systemctl status nginx
```

***

## SSL (HTTPS)





***

## Basic Auth + Nginx

```bash
sudo apt-get update -y && sudo apt-get upgrade -y
```

```bash
sudo apt install -y apache2-utils
```

```bash
PASSWORD=$(openssl rand -base64 15)
```

```bash
sudo htpasswd -c /etc/nginx/conf.d/.htpasswd admin
```

```bash
echo "Basic Auth user: admin"
echo "Basic Auth password: $PASSWORD"
```

Check:

```bash
cat /etc/nginx/conf.d/.htpasswd
```

Set Basic Auth to MOODLE host:

```bash
sudo nano /etc/nginx/sites-available/moodle
```

```
auth_basic "Enter password!";
auth_basic_user_file /etc/nginx/conf.d/.htpasswd;
```

Save: Ctrl+X, Enter

```bash
nginx -t
```

```bash
sudo service nginx restart
```

[^1]: Self domain
