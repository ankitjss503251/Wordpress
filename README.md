# Wordpress
# Deploying WordPress with MySQL on an Nginx server involves several steps. Here’s a comprehensive guide to help you through the process:
# Prerequisites
# A server running a compatible OS (like Ubuntu).
# Access to the server (via SSH if remote).
# Root or sudo privileges on the server.
1. Install Nginx
# First, update your package list and install Nginx:
sudo apt update
sudo apt install nginx

# After installation, you can start and enable Nginx to run on boot:
sudo systemctl start nginx
sudo systemctl enable nginx

2. Install MySQL
# Install MySQL server:
sudo apt install mysql-server

# Secure your MySQL installation:
sudo mysql_secure_installation

# Follow the prompts to set a root password and configure security settings.
3. Install PHP and Required Extensions
# WordPress requires PHP along with a few extensions. Install PHP and the necessary extensions:
sudo apt install php-fpm php-mysql php-xml php-mbstring

4. Configure PHP for Nginx
# Open the PHP configuration file for editing:
sudo nano /etc/php/7.4/fpm/php.ini

# Ensure the cgi.fix_pathinfo setting is configured as follows:
ini
Copy code
cgi.fix_pathinfo=0

# Save and close the file, then restart PHP-FPM:
sudo systemctl restart php7.4-fpm

5. Set Up MySQL Database for WordPress
# Log in to MySQL:
sudo mysql -u root -p

# Create a database and user for WordPress:
CREATE DATABASE Wordpress_DB;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

# Replace 'password' with a strong password of your choice.
6. Download and Configure WordPress
# Navigate to the directory where you want to install WordPress (e.g., /var/www/html):
cd /var/www/html

# Download WordPress:
wget https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz

# Move the WordPress files to the root directory:
sudo mv wordpress/* ./
sudo rmdir wordpress

# Set the correct permissions:
sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 750 {} \;
sudo find /var/www/html -type f -exec chmod 640 {} \;

7. Configure Nginx for WordPress
# Create a new Nginx configuration file for your WordPress site:
sudo nano /etc/nginx/sites-available/wordpress

# Add the following configuration:
cd /etc/nginx/conf.d
nano nginx-wordpress.conf 
server {
    listen 80;
    server_name example.com;  # Replace with your domain or IP address

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}

# Enable the new configuration and test Nginx for syntax errors:
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
sudo nginx -t

# Reload Nginx to apply the changes:
sudo systemctl reload nginx

8. Complete WordPress Installation
# Open your web browser and navigate to your domain or server IP (e.g., http://example.com).
# You should see the WordPress setup page. Follow the on-screen instructions to complete the installation:
# Select your language.
# Enter the database details you created earlier.
# Complete the installation by setting up your site title, username, password, and email.
# Once completed, you should be able to log in to your WordPress admin dashboard.
# Troubleshooting
# Check Logs: If something isn’t working, check Nginx and PHP logs for errors:
# Nginx logs: /var/log/nginx/error.log
# PHP logs: /var/log/php7.4-fpm.log
# File Permissions: Ensure that file and directory permissions are correctly set.
# By following these steps, you should have a functional WordPress site running on Nginx with MySQL

