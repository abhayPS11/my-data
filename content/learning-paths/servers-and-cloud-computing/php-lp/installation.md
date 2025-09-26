 Installation of PHP on Ubuntu ARM

1. Update the system

```console
sudo apt update && sudo apt upgrade -y
```

2. Install PHP, Apache, and common extensions

```console
sudo apt install php php-cli php-fpm php-mysql apache2 libapache2-mod-php -y
```

3. Verify PHP installation

```console
php -v
```
You should see output similar to:
```output
PHP 8.3.6 (cli) (built: Jul 14 2025 18:30:55) (NTS)
Copyright (c) The PHP Group
Zend Engine v4.3.6, Copyright (c) Zend Technologies
with Zend OPcache v8.3.6, Copyright (c), by Zend Technologies
```
