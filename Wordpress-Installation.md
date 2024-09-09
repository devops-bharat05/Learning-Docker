# WordPress Installation on Ubuntu EC2 Instance

This guide will walk you through installing and configuring WordPress on an EC2 instance running Ubuntu. Follow these steps carefully to set up a functional WordPress website.

## Prerequisites

- An active AWS account.
- An EC2 instance running Ubuntu (minimum t2.micro for free tier).
- A domain name (optional but recommended).
- SSH access to your EC2 instance.
- Basic knowledge of Linux commands.

## Step 1: Update Your Server

After logging into your EC2 instance via SSH, the first step is to update the package repository index:

```bash
sudo apt update && sudo apt upgrade -y
```

## Step 2: Install Apache Web Server

Install Apache, a popular and reliable web server, and enable it to start on boot:

```bash
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

To verify Apache is installed correctly, visit your EC2 instance's public IP address in your browser. You should see the default Apache2 Ubuntu welcome page.

## Step 3: Install MySQL (Database Server)

WordPress requires a database to store its information. Install MySQL by running the following command:

```bash
sudo apt install mysql-server -y
```

After installation, secure your MySQL instance:

```bash
sudo mysql_secure_installation
```

Follow the prompts to enhance the security of MySQL, setting a strong password for the root account and answering `Y` for the remaining options.

## Step 4: Install PHP and Required Modules

WordPress is written in PHP, so you need to install PHP and several necessary extensions:

```bash
sudo apt install php libapache2-mod-php php-mysql php-xml php-mbstring php-curl php-gd -y
```

Restart Apache to load PHP:

```bash
sudo systemctl restart apache2
```

## Step 5: Create a MySQL Database and User for WordPress

Log in to the MySQL shell:

```bash
sudo mysql
```

Inside the MySQL shell, run the following SQL commands to create a database and a user for WordPress:

```sql
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Make sure to replace `your_password` with a strong password.

## Step 6: Download WordPress

Navigate to the `/tmp` directory and download the latest WordPress package:

```bash
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
```

Extract the downloaded archive:

```bash
tar xzvf latest.tar.gz
```

Move the extracted files to the `/var/www/html` directory:

```bash
sudo mv /tmp/wordpress /var/www/html/wordpress
```

Set the correct permissions:

```bash
sudo chown -R www-data:www-data /var/www/html/wordpress
sudo chmod -R 755 /var/www/html/wordpress
```

## Step 7: Configure Apache for WordPress

Create a new Apache configuration file for WordPress:

```bash
sudo nano /etc/apache2/sites-available/wordpress.conf
```

Paste the following configuration into the file:

```conf
<VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/html/wordpress
    ServerName your_domain_or_ip
    <Directory /var/www/html/wordpress/>
        AllowOverride All
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Replace `your_domain_or_ip` with your EC2 instance's public IP or domain.

Enable the new site and the `mod_rewrite` module:

```bash
sudo a2ensite wordpress
sudo a2enmod rewrite
sudo systemctl restart apache2
```

## Step 8: Complete the WordPress Installation Through the Web Interface

Visit your EC2 instance's public IP or domain in your browser, and you'll be greeted by the WordPress setup page. Choose your language, and proceed with the installation. You will be asked to enter the database details:

- **Database Name**: `wordpress`
- **Username**: `wordpressuser`
- **Password**: `your_password`
- **Database Host**: `localhost`
- **Table Prefix**: `wp_` (you can leave this as is)

Click "Submit," and WordPress will complete the installation. Follow the prompts to create your admin user and set up your site.

## Step 9: Secure Your WordPress Site (Optional)

1. **Install SSL with Let’s Encrypt**:
   If you are using a domain, secure your site with SSL:

   ```bash
   sudo apt install certbot python3-certbot-apache -y
   sudo certbot --apache -d your_domain -d www.your_domain
   ```

   Follow the prompts to configure SSL.

2. **Update Firewall Rules**:
   Allow HTTP and HTTPS traffic through your EC2 instance:

   ```bash
   sudo ufw allow in "Apache Full"
   sudo ufw enable
   ```

## Conclusion

Congratulations! You have successfully installed WordPress on your EC2 instance running Ubuntu. You can now log in to the WordPress admin panel by visiting `http://your_domain_or_ip/wp-admin/`. From here, you can start customizing your website and adding content.
```

### Notes:
- Replace placeholders such as `your_password`, `your_domain_or_ip`, and `admin@example.com` with actual values specific to your environment.
- You might also want to customize the security settings based on your needs, such as using SSH for SFTP and configuring firewalls (UFW) further.
