# Day 15: Setup SSL for Nginx

## Challenge Description
The system admins team of xFusionCorp Industries needs to deploy a new application on App Server 2 (`stapp02`) in Stratos Datacenter. We need to prepare the server with the following requirements:

1.  Install and configure Nginx on App Server 2.
2.  Move the self-signed SSL certificate and key from `/tmp/nautilus.crt` and `/tmp/nautilus.key` to an appropriate location and deploy them in Nginx.
3.  Create an `index.html` file with the content "Welcome!" under the Nginx document root.
4.  Verify the setup by accessing the server via HTTPS using `curl` from the jump host.

## Key Concepts
*   **Nginx Installation:** Installing the web server via package manager (yum/epel).
*   **SSL Configuration:** Configuring Nginx to serve traffic over HTTPS (port 443) using SSL certificates.
*   **Certificate Management:** Securely storing and referencing SSL certificates and keys.

## Solution

We performed the following steps on **App Server 2** (`stapp02`).

### 1. Install Nginx
First, we installed the EPEL repository and then the Nginx web server.

```bash
# Connect to stapp02
ssh steve@stapp02

# Install EPEL and Nginx
sudo yum install epel-release -y
sudo yum install nginx -y
```

### 2. Configure SSL Certificates
We created a directory for the SSL files and moved them from the temporary location.

```bash
# Create directory
sudo mkdir -p /etc/nginx/ssl

# Move certificate and key
sudo mv /tmp/nautilus.crt /etc/nginx/ssl/
sudo mv /tmp/nautilus.key /etc/nginx/ssl/
```

### 3. Create Web Content
We created the required index file in the default document root.

```bash
sudo bash -c 'echo "Welcome!" > /usr/share/nginx/html/index.html'
```

### 4. Configure Nginx for HTTPS
We created a new configuration file `/etc/nginx/conf.d/nautilus.conf` to handle the SSL traffic.

```bash
sudo vi /etc/nginx/conf.d/nautilus.conf
```

**Content of `nautilus.conf`:**
```nginx
server {
    listen 443 ssl;
    server_name localhost;

    ssl_certificate /etc/nginx/ssl/nautilus.crt;
    ssl_certificate_key /etc/nginx/ssl/nautilus.key;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```

### 5. Start Nginx
We verified the configuration syntax and started the service.

```bash
# Check syntax
sudo nginx -t

# Start and enable Nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

## Verification
From the **Jump Host**, we verified the setup using `curl`.

```bash
curl -Ik https://172.16.238.11/
```

**Expected Output:**
The output showed `HTTP/1.1 200 OK` (or similar success status) and indicated that the connection was secure, confirming the SSL setup was working correctly.
