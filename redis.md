# Redis



```bash
sudo apt-get update -y && sudo apt-get upgrade -y
```

```bash
sudo apt install -y redis-server
```

```bash
cp /etc/redis/redis.conf /etc/redis/moodle.conf
```

```bash
sudo systemctl enable redis-server@moodle.service
```

```bash
sudo service redis-server status
```

Configuration options in config.php:

```php
$CFG->session_handler_class = '\core\session\redis';
$CFG->session_redis_host = '127.0.0.1';
$CFG->session_redis_port = 6379;  // Optional.
$CFG->session_redis_database = 0;  // Optional, default is db 0.
$CFG->session_redis_auth = ''; // Optional, default is don't set one.
$CFG->session_redis_prefix = ''; // Optional, default is don't set one.
$CFG->session_redis_acquire_lock_timeout = 120;
$CFG->session_redis_acquire_lock_retry = 100; // Optional, default is 100ms (from 3.9)
$CFG->session_redis_lock_expire = 7200;
$CFG->session_redis_serializer_use_igbinary = false; // Optional, default is PHP builtin serializer.
```
