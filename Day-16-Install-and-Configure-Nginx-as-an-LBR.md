# Day 16: Install and Configure Nginx as an LBR

## Challenge Description
Traffic is increasing on one of the websites managed by the Nautilus production support team, causing performance degradation. The team decided to migrate the application to a high availability stack using a Load Balancer. The migration is mostly done, but the LBR server configuration is pending.

We need to:
1.  Install Nginx on the LBR (Load Balancer) server (`stlb01`).
2.  Configure load-balancing within the `http` context in `/etc/nginx/nginx.conf`, utilizing all App Servers (`stapp01`, `stapp02`, `stapp03`).
3.  Ensure Apache is running on all app servers and is reachable (we identified the port as **8085**).
4.  Verify access via the Load Balancer.

## Key Concepts
*   **Load Balancing:** Distributing network traffic across multiple servers to ensure reliability and performance.
*   **Nginx Upstream Module:** Defines groups of servers that can be referenced by the `proxy_pass`, `fastcgi_pass`, etc., directives.
*   **Reverse Proxy:** Nginx acting as an intermediary for requests from clients seeking resources from the backend servers.

## Solution

We performed the following steps on the Load Balancer Server (`stlb01`).

### 1. Install Nginx
First, we installed the EPEL repository (if not present) and then the Nginx package.

```bash
# Connect to stlb01
ssh loki@stlb01

# Install EPEL and Nginx
sudo yum install epel-release -y
sudo yum install nginx -y
```

### 2. Configure Nginx Load Balancing
We edited the main configuration file `/etc/nginx/nginx.conf`. We located the `http` block and added the `upstream` group pointing to the App Servers on port **8085** (which we verified was the port Apache listens on). We also configured the `server` block to proxy requests to this upstream group.

```bash
sudo vi /etc/nginx/nginx.conf
```

**Configuration changes inside `http { ... }`:**

```nginx
http {
    # ... existing config ...

    upstream backend_servers {
        server 172.16.238.10:8085; # stapp01
        server 172.16.238.11:8085; # stapp02
        server 172.16.238.12:8085; # stapp03
    }

    server {
        listen       80;
        server_name  localhost;

        location / {
            proxy_pass http://backend_servers;
        }
        
        # ... other default settings ...
    }
}
```

### 3. Start Nginx
We verified the syntax and started the service.

```bash
# Check syntax
sudo nginx -t

# Start and enable service
sudo systemctl start nginx
sudo systemctl enable nginx
```

## Verification
We verified the setup by sending requests to the Load Balancer's IP (or localhost from within `stlb01`) and observing that the content was served.

```bash
curl -I http://localhost
```

The application is now accessible via the Load Balancer, which distributes traffic to the three application servers.
