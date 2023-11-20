# Deploy CodeIgniter On NGINX

## Whats is CodeIgniter?
CodeIgniter is a powerful PHP framework with a very small footprint, built for developers who need a simple and elegant toolkit to create full-featured web applications. [official site](http://codeigniter.com).

## Requirements

App : 
- Install Nginx or Apache Server
- Install PHP-fpm (FastCGI Process Manager)
- Install Mysql (Optional)
- Install Composer

PHP version 5.6 

## Step by Step How To Install NGINX , PHP

- Install NGINX Server : 

` sudo apt update`

` sudo apt install nginx`

- Adjusting Firewall :

` sudo ufw app list`
  
` sudo ufw allow 'Nginx HTTP'`

` sudo ufw allow 'Nginx HTTPS'`

` sudo ufw status`
 
- Check Server : 

` systemctl status nginx`


*if Nginx is already running, the status will appear as shown above

- If Nginx has not started yet  : 

` sudo systemctl start nginx`

- Install PHP-fpm :

` sudo apt-get install python-software-properties`

` sudo add-apt-repository ppa:ondrej/php`

` sudo apt-get update`

` sudo apt-get install php5.6-fpm php5.6-gd php5.6-mysql php5.6-imap php5.6-curl php5.6-intl php5.6-pspell php5.6-recode php5.6-sqlite3 php5.6-tidy php5.6-xmlrpc php5.6-xsl php5.6-zip php5.6-mbstring php5.6-soap php5.6-opcache php5.6-common php5.6-json php5.6-readline php5.6-xml`


- Change Access to folder `var/www/html` : 

` sudo chmod -R 755 /var/www/html/code_ingiter_files`

` sudo nano /etc/nginx/sites-available/default`

- Change config on file `/etc/nginx/sites-available/default` :

Add this line on Server block : `index index.php index.html index.htm index.nginx-debian.html;`

PUse this Line below on PHP Block : 

```php
location ~ \.php$ {
    include snippets/fastcgi-php.conf;  
    fastcgi_pass unix:/run/php/php5.6-fpm.sock; 
}
```

- To check whether the PHP is running or not, go to `/var/www/html/` :

Then create info.php file :

```php
<?php
  phpinfo();
php?>
```

If PHP is already running, there is an info for your php on your nginx server on `localhost/info.php`

## Step by Step to Deploy / Install CodeIgniter on NGINX

`sudo /etc/nginx/sites-available/codeigniter.conf`

- Create Config file on `/etc/nginx/sites-available/codeigniter.conf` : 

```php

server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /var/www/html/Code_iginter_files;

	# Add index.php to the list if you are using PHP
	index index.php index.html index.htm;

	server_name _;

location / {
	try_files $uri $uri/ /index.php;
}

	# pass PHP scripts to FastCGI server
	#
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/run/php/php5.6-fpm.sock;
	}

	# deny access to .htaccess files, if Apache's document root

	location ~ /\.ht {
		deny all;
	}
}



```

- Create Link to sites-enabled :

`sudo ln -s /etc/nginx/sites-available/codeigniter.conf /etc/nginx/sites-enabled/`

`sudo nginx -t`

`sudo systemctl restart nginx`

## Step by Step How To Install Mysql 5.7 

## Reference

https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04

https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-ubuntu-18-04
