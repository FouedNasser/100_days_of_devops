# Day 19: Install and Configure Web Application

## Challenge Description
xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter. The development of these websites is still in-progress, but we want to get the servers ready.

The task requires performing the following steps on **App Server 2**:
a. Install `httpd` package and dependencies.
b. Configure Apache to serve on port `8085`.
c. Deploy two website backups located at `/home/thor/blog` and `/home/thor/apps` on `jump_host`.
   - `blog` should be accessible at `http://localhost:8085/blog/`
   - `apps` should be accessible at `http://localhost:8085/apps/`
d. Verify access using `curl`.

## Key Concepts
*   **SCP (Secure Copy):** Used to securely transfer files between hosts.
*   **Apache HTTP Server (httpd):** A widely used open-source web server.
*   **Linux File System Hierarchy:** Understanding `/var/www/html` as the default document root.
*   **Service Management (systemctl):** Starting and enabling services.
*   **Port Configuration:** Changing default listening ports for services.

## Solution

### 1. Transfer Website Data to App Server 2
First, we transfer the website content from the `jump_host` to `stapp02`. We place it in `/tmp` initially.

```bash
scp -r /home/thor/blog steve@stapp02:/tmp/
scp -r /home/thor/apps steve@stapp02:/tmp/
```
*Note: The password for user `steve` is `Am3ric@`.*

### 2. Connect to App Server 2
Login to the target application server.

```bash
ssh steve@stapp02
```
*Password: `Am3ric@`*

### 3. Install Apache Web Server
Install the `httpd` package.

```bash
sudo yum install httpd -y
```

### 4. Configure Apache Port
Modify the configuration to listen on port `8085` instead of the default `80`.

```bash
sudo sed -i 's/Listen 80/Listen 8085/g' /etc/httpd/conf/httpd.conf
```
*Alternatively, edit `/etc/httpd/conf/httpd.conf` using `vi` and change `Listen 80` to `Listen 8085`.*

### 5. Deploy Website Content
Move the directories from `/tmp` to the Apache document root `/var/www/html`. This automatically makes them available at the `/blog` and `/apps` paths.

```bash
sudo mv /tmp/blog /var/www/html/
sudo mv /tmp/apps /var/www/html/
```

### 6. Start and Enable Service
Start the httpd service and enable it to persist across reboots.

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

## Verification
Verify the configuration by making requests to the locally hosted sites using `curl`.

```bash
curl http://localhost:8085/blog/
curl http://localhost:8085/apps/
```
Both commands should return the HTML content of the respective sites.
