# Manual WordPress Installation (No Control Panel)

A complete production guide to manually installing WordPress on a clean Linux server using Nginx, PHP-FPM, and MySQL/MariaDB.

---

### Step 1: Create the MySQL Database & User
Log into your database management shell as root:
```bash
sudo mysql -u root -p
```

Execute these queries sequentially to create an isolated database and dedicated database user:
```sql
-- 1. Create a clean database with secure UTF-8 character mapping
CREATE DATABASE wordpress_db DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 2. Create a local system user with a strong password
CREATE USER 'wp_db_user'@'localhost' IDENTIFIED BY 'your_highly_secure_password';

-- 3. Grant the user full execution permissions over its specific database
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_db_user'@'localhost';

-- 4. Flush cache mappings and exit the shell
FLUSH PRIVILEGES;
EXIT;
```

### Step 2: Download and Extract WordPress Core
Download the latest official package directly from WordPress.org into your server's temporary cache directory, extract it, and move it to your active `/var/www/` workspace:

```bash
# 1. Download and extract
cd /tmp
wget https://wordpress.org
tar -xzvf latest.tar.gz

# 2. Build your target website directory
sudo mkdir -p /var/www/my-manual-wp

# 3. Move the core files into position
sudo cp -r /tmp/wordpress/* /var/www/my-manual-wp/
```

### Step 3: Establish Linux Web Permissions
To ensure you can download plugins, upload images, and update themes directly through the WordPress dashboard without being blocked or prompted for FTP details, assign ownership to the Nginx engine (`www-data`):

```bash
# 1. Give the Nginx user full ownership of the workspace
sudo chown -R www-data:www-data /var/www/my-manual-wp

# 2. Enforce secure directory and file modes recursively
sudo find /var/www/my-manual-wp/ -type d -exec chmod 755 {} \;
sudo find /var/www/my-manual-wp/ -type f -exec chmod 644 {} \;
```

### Step 4: Configure the Nginx Server Block
Create a clean Nginx routing layout file:
```bash
sudo nano /etc/nginx/sites-available/my-manual-wp
```

Paste the following ruleset. Make sure to update **`server_name`** and confirm your **`fastcgi_pass`** points to your exact PHP-FPM socket version (e.g., `php8.2-fpm.sock` or `php8.3-fpm.sock`):

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name yourdomain.com ://yourdomain.com;
    root /var/www/my-manual-wp;

    index index.php index.html index.htm;
    charset utf-8;

    # Core URL rewrites for clean WordPress permalinks
    location / {
        try_files \$uri \$uri/ /index.php?\$args;
    }

    # Pass PHP executions directly to the PHP-FPM processor pool
    location ~ \.php\$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME \$document_root\$fastcgi_script_name;
        include fastcgi_params;
    }

    # Security: Prevent executing scripts from the uploads directory
    location ~* /(?:uploads|files)/.*\.php\$ {
        deny all;
    }

    # Performance: Static asset optimization
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|otf)\$ {
        expires max;
        log_not_found off;
    }

    # Deny access to hidden system management files (.git, .htaccess)
    location ~* /\. {
        deny all;
    }

    location = /favicon.ico { log_not_found off; access_log off; }
    location = /robots.txt  { log_not_found off; access_log off; allow all; }
}
```

### Step 5: Activate and Launch the Site Configuration
Enable the server block by symlinking it into Nginx's active directory, check for typos, and reload the web server service:

```bash
# 1. Enable the site profile
sudo ln -s /etc/nginx/sites-available/my-manual-wp /etc/nginx/sites-enabled/

# 2. Test configuration files for architectural errors
sudo nginx -t

# 3. Reload Nginx without causing downtime
sudo systemctl reload nginx
```

### Step 6: Finalize Installation inside your Browser
Open your browser and navigate to `http://yourdomain.com`. Follow the interactive installation wizard using the credentials you generated in **Step 1**:
*   **Database Name:** `wordpress_db`
*   **Username:** `wp_db_user`
*   **Password:** `your_highly_secure_password`
*   **Database Host:** `localhost`

---
### 💡 Post-Install Hardening
Once your website setup finishes, lock down your central configuration file for advanced security:
```bash
sudo chmod 640 /var/www/my-manual-wp/wp-config.php
```
