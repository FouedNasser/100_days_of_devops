# Day 20: Configure Nginx + PHP-FPM Using Unix Socket

## Challenge Description
The Nautilus application development team is planning to launch a new PHP-based application, which they want to deploy on Nautilus infra in Stratos DC. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure.

The requirements are:
a. Install Nginx on App Server 2 (`stapp02`), configure it to use port `8094`, and its document root should be `/var/www/html`.
b. Install PHP-FPM version 8.2 on App Server 2, it must use the Unix socket `/var/run/php-fpm/default.sock` (create the parent directories if they don't exist).
c. Configure PHP-FPM and Nginx to work together.
d. Once configured correctly, verify the website using `curl http://stapp02:8094/index.php` command from `jump_host`.

NOTE: We have copied two files, `index.php` and `info.php`, under `/var/www/html` as part of the PHP-based application setup. These files should not be modified.

## Key Concepts
*   **Nginx:** A high-performance web server and reverse proxy.
*   **PHP-FPM (FastCGI Process Manager):** An alternative PHP FastCGI implementation with some additional features useful for heavily loaded sites.
*   **Unix Socket:** An endpoint for inter-process communication (IPC) within the same operating system kernel, offering lower overhead than TCP/IP sockets for local communication.
*   **`dnf`:** The next-generation package manager for RPM-based Linux distributions.
*   **Remi Repository:** A third-party YUM/DNF repository providing updated PHP versions.
*   **`systemctl`:** Command-line utility to control systemd system and service manager.

## Solution

### 1. Install Nginx
We install Nginx to handle HTTP requests.
```bash
sudo dnf install -y nginx
```
*Optional: Verify Nginx version:*
```bash
nginx -v
```

### 2. Install PHP 8.2 + PHP-FPM
Nginx cannot run PHP by itself; PHP-FPM is the PHP engine that processes PHP code. We install PHP 8.2 using the Remi repository.

First, enable the required repositories:
```bash
sudo dnf install -y epel-release
sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
```
Then, enable the PHP 8.2 module and install PHP + PHP-FPM:
```bash
sudo dnf module reset php -y
sudo dnf module enable php:remi-8.2 -y
sudo dnf install -y php php-fpm
```

### 3. Configure PHP-FPM Socket
Nginx and PHP-FPM will communicate through a Unix socket for efficiency and security.

1.  **Create the socket directory:**
    ```bash
    sudo mkdir -p /var/run/php-fpm
    ```

2.  **Edit the PHP-FPM pool configuration file:**
    ```bash
    sudo vi /etc/php-fpm.d/www.conf
    ```
    Modify or ensure the following lines are set correctly to configure the user/group for the PHP-FPM process and the Unix socket:
    ```ini
    user = nginx
    group = nginx

    listen = /var/run/php-fpm/default.sock
    listen.owner = nginx
    listen.group = nginx
    listen.mode = 0660
    ```
    *Note: The `user` and `group` should be set to `nginx` to ensure Nginx has proper permissions to interact with the socket. Save and exit (`:wq`).*

### 4. Start PHP-FPM Service
The Unix socket will only exist once PHP-FPM is running.
```bash
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
```
*Verify the socket exists and has correct permissions:*
```bash
ls -l /var/run/php-fpm/default.sock
```

### 5. Configure Nginx for the PHP Application
Instead of modifying `nginx.conf` directly, we create a new configuration file in `conf.d` for better organization.

1.  **Create a new server configuration file:**
    ```bash
    sudo vi /etc/nginx/conf.d/phpapp.conf
    ```
    Paste the following configuration:
    ```nginx
    server {
        listen 8094;
        server_name stapp02; # Or your server's IP address
        root /var/www/html;
        index index.php index.html;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            fastcgi_pass unix:/var/run/php-fpm/default.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
    ```
    *Important: Ensure there is no `try_files` directive within the `location ~ \.php$` block, as this can cause 404 errors for PHP files.*
    *Save and exit (`:wq`).*

### 6. Test Nginx Configuration
Always test the Nginx configuration for syntax errors before restarting the service.
```bash
sudo nginx -t
```
*Expected output:*
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

### 7. Restart Nginx Service
Apply the new Nginx configuration.
```bash
sudo systemctl restart nginx
sudo systemctl enable nginx
```
*Verify Nginx is listening on port 8094:*
```bash
ss -lntp | grep 8094
```

## Verification

From the **jump host**, execute the following `curl` commands to verify the PHP application is working correctly:

```bash
curl http://stapp02:8094/index.php
curl http://stapp02:8094/info.php
```
You should see the rendered PHP output (e.g., HTML content from `index.php` or the PHP information page from `info.php`), not raw PHP code or 404 errors.
