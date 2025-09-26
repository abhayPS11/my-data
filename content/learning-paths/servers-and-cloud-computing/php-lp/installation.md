 Installation of PHP on  SUSE ARM (GCP VM)

1. Update the system

```console
sudo zypper refresh
sudo zypper update -y
```

2. Install PHP, Apache, and common extensions

```console
sudo zypper install -y php php-cli php-fpm php-mysql php-xml php-mbstring php-opcache apache2
```

3. Enable and start Apache:

```console
sudo systemctl enable apache2
sudo systemctl start apache2
sudo systemctl status apache2
```

4. Verify PHP installation

```console
php -v
```
You should see output similar to:
```output
PHP 8.0.30 (cli) (built: Nov 25 2024 12:00:00) ( NTS )
Copyright (c) The PHP Group
Zend Engine v4.0.30, Copyright (c) Zend Technologies
with Zend OPcache v8.0.30, Copyright (c), by Zend Technologies
```
