#!/bin/bash
#----------------------------------------
# PRIME MURCIA
# AUTO Install LEMP
#----------------------------------------

# Update and Upgrade your packages
apt-get update -y -q

# Install nginx in Ubuntu
apt-get install nginx -y -q

# Configure UFW to allow Nginx in UFW
sudo ufw allow 'Nginx HTTP'
sudo ufw allow 'Nginx HTTPS'

# Add PHP repository and install a specific PHP version
apt-get install python-software-properties
add-apt-repository ppa:ondrej/php
apt-get update

echo "Creating PHP Configure"
php_version="5.6"
echo "Installing PHP $php_version ......"
apt-get install php$php_version-fpm

# Note: Enter full name of Web app
unlink /etc/nginx/sites-enabled/default
echo "Creating WebApp Nginx Configure for Code Igniter"
read -p "Enter WebApp Name: " webapp_name
read -p "Enter web domain: " domain_name
echo "Name of WebApp: $webapp_name  Web domain: $domain_name"

website_config="/etc/nginx/sites-available/$webapp_name"
server_block="
server {
        listen 80;
        root /var/www/html/$webapp_name;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name $domain_name;

        location / {
                try_files \$uri \$uri/ /index.php;
        }
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php$php_version-fpm.sock;
        }
}"

# Create the new website configuration file
echo "$server_block" | sudo tee "$website_config" > /dev/null

# Create a symbolic link to enable the site
sudo ln -s "$website_config" "/etc/nginx/sites-enabled/"

# Test the Nginx configuration for syntax errors
sudo nginx -t

# If the syntax test is successful, reload Nginx to apply changes
if [ $? -eq 0 ]; then
    sudo systemctl reload nginx
    echo "New website configuration added and Nginx reloaded."
else
    echo "Error: Nginx configuration test failed. No changes applied."
fi

# Install PHP extensions
apt-get install php$php_version-gd php$php_version-mysql php$php_version-imap php$php_version-curl php$php_version-intl php$php_version-pspell php$php_version-recode php$php_version-sqlite3 php$php_version-tidy php$php_version-xmlrpc php$php_version-xsl php$php_version-zip php$php_version-mbstring php$php_version-soap php$php_version-opcache php$php_version-common php$php_version-json php$php_version-readline php$php_version-xml

# Note: Edit the MySQL Server version needed (Only 5.7 is supported in this script)
echo "Creating MySQL Server Configure"
sql_version="5.7"
echo "Installing MySQL Server: $sql_version version......"
wget https://dev.mysql.com/get/mysql-apt-config_0.8.12-1_all.deb
dpkg -i mysql-apt-config_0.8.12-1_all.deb
apt update
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 467B942D3A79BD29
apt update
apt-cache policy mysql-server
apt install -f mysql-client=5.7* mysql-community-server=5.7* mysql-server=5.7*

# Install SSH Port
read -p "Enter the new SSH port: " new_port

# Verify if the input is a valid port number
if [[ "$new_port" =~ ^[0-9]+$ ]]; then
    # Backup the SSH configuration file
    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak

    # Use sed to change the SSH port in the configuration file
    sudo sed -i "s/Port [0-9]*/Port $new_port/" /etc/ssh/sshd_config

    # Restart the SSH service to apply the changes
    sudo service ssh restart

    echo "SSH port changed to $new_port. Don't forget to update your firewall rules if necessary."
else
    echo "Invalid port number. Please enter a valid port number."
fi
