Installation of PHP on SUSE ARM (GCP VM)
1. Update the system
sudo zypper refresh
sudo zypper update -y

2. Install PHP, PHP-FPM, and common extensions
sudo zypper install -y php php-cli php-fpm php-mysql php-xml php-mbstring php-opcache

3. Install Apache
sudo zypper install -y apache2


Enable and start Apache:

sudo systemctl enable apache2
sudo systemctl start apache2
sudo systemctl status apache2

4. Verify PHP installation
php -v


You should see output like:

PHP 8.x.x (cli) (built: <date>) (NTS)
Copyright (c) The PHP Group
Zend Engine v4.x.x, Copyright (c) Zend Technologies
with Zend OPcache v8.x.x, Copyright (c), by Zend Technologies

Baseline Setup for PHP-FPM
5. Configure PHP-FPM pool

Copy the default pool if not present:

sudo cp /etc/php8/fpm/php-fpm.d/www.conf.default /etc/php8/fpm/php-fpm.d/www.conf


Edit the pool configuration:

sudo nano /etc/php8/fpm/php-fpm.d/www.conf


Change it to use a Unix socket:

; listen = 127.0.0.1:9000
listen = /run/php-fpm/www.sock
listen.owner = wwwrun
listen.group = www
listen.mode = 0660


wwwrun is the default Apache user on SUSE.

0660 ensures Apache can read/write the socket.

6. Start and enable PHP-FPM
sudo systemctl restart php-fpm
sudo systemctl enable php-fpm


Check if it’s running:

sudo systemctl status php-fpm --no-pager -l


Verify the socket exists:

ls -l /run/php-fpm/www.sock

7. Test PHP

Create an info page:

echo "<?php phpinfo(); ?>" | sudo tee /srv/www/htdocs/info.php


Test locally:

curl http://localhost/info.php


Test from browser:

http://<YOUR_VM_PUBLIC_IP>/info.php


✅ You should see the full PHP info page.
