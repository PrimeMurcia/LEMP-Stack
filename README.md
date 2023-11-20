# Deploy CodeIgniter On NGINX

## Whats is CodeIgniter?
CodeIgniter is a powerful PHP framework with a very small footprint, built for developers who need a simple and elegant toolkit to create full-featured web applications. [official site](http://codeigniter.com).

## Requirements

App : 
- Install Nginx 
- Install PHP-fpm (FastCGI Process Manager)
- Install Mysql

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

![Image of NGINX deploy](/image/nginx_status.png)

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

### 1  Step 1: Add the MySQL 5.7 APT Repository

- 1.1 Download the MySQL repository by executing the following command:

`wget https://dev.mysql.com/get/mysql-apt-config_0.8.12-1_all.deb`

![Image of NGINX deploy](/image/mysql1.png)

- 1.2 After the MySQL package has been successfully downloaded, install it:

`sudo dpkg -i mysql-apt-config_0.8.12-1_all.deb`

![Image of NGINX deploy](/image/mysql2.png)

- 1.3 Next, select Ubuntu Bionic.

![Image of NGINX deploy](/image/mysql3.png)

- After that, select the MySQL Server & Cluster option. Then, select mysql-5.7 and finally select Ok.

![Image of NGINX deploy](/image/mysql4.png)

- 1.4 Next, update the APT repository:

![Image of NGINX deploy](/image/mysql5.png)

* Note: If you encounter the "signature couldn't be verified" error like this one: NO_PUBKEY 467B942D3A79BD29, you will need to import the missing gpg key by running the following command:

`sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 467B942D3A79BD29`

- 1.5 Then execute the apt update again:

`sudo apt update`

- To check whether MySQL 5.7 repository has been successfully installed, execute:

`sudo apt-cache policy mysql-server`

- You should see MySQL 5.7 repository at the bottom of the list.

![Image of NGINX deploy](/image/mysql6.png)

### Step 2: Install MySQL 5.7

- 2.1 Now that you have a MySQL 5.7 repository in your system, you can proceed to install it. For this, run the following command:

`sudo apt install -f mysql-client=5.7* mysql-community-server=5.7* mysql-server=5.7*1`

- 2.2 Press ENTER to begin the installation and set the root password when asked.
  
## Reference

[https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04](https://ajayogal.wordpress.com/2017/06/05/how-to-install-linux-nginx-mysql-php-5-6-lemp-stack-on-ubuntu-16-04/)https://ajayogal.wordpress.com/2017/06/05/how-to-install-linux-nginx-mysql-php-5-6-lemp-stack-on-ubuntu-16-04/

https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-ubuntu-18-04

https://www.devart.com/dbforge/mysql/how-to-install-mysql-on-ubuntu/
