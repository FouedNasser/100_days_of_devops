# Day 14: Linux Process Troubleshooting

## Challenge Description
The production support team of xFusionCorp Industries has deployed monitoring tools that reported Apache service unavailability on one of the app servers in Stratos DC.

Our task is to:
1.  Identify the faulty app host and fix the issue.
2.  Make sure the Apache service is up and running on all app hosts.
3.  Configure Apache to run on port **6000** on all app servers.

## Key Concepts
*   **Process Management:** Identifying and managing running processes.
*   **Port Conflicts:** Troubleshooting when two services attempt to bind to the same port.
*   **Apache Configuration:** Modifying the `Listen` directive in `httpd.conf`.
*   **Service Management:** Using `systemctl` to start, stop, and restart services.

## Solution

We performed the following steps on all App Servers (`stapp01`, `stapp02`, `stapp03`).

### 1. Configure Apache to Listen on Port 6000
On all three servers, we updated the Apache configuration file.

```bash
# Connect to the server
ssh <user>@<host>

# Edit the configuration
sudo vi /etc/httpd/conf/httpd.conf

# Find 'Listen 80' and change it to:
Listen 6000

# Save and exit
```

### 2. Restart Apache on Healthy Hosts (stapp02, stapp03)
On `stapp02` and `stapp03`, simply restarting the service applied the changes successfully.

```bash
sudo systemctl restart httpd
sudo systemctl status httpd
```

### 3. Troubleshoot Faulty Host (stapp01)
On `stapp01`, the service failed to start after the configuration change.

**Diagnosis:**
We checked if the port was already in use.

```bash
sudo netstat -tulpn | grep 6000
```
Output indicated that the **sendmail** service was listening on port 6000, causing a conflict.

**Fix:**
We stopped the conflicting service and restarted Apache.

```bash
# Stop sendmail
sudo systemctl stop sendmail

# Restart Apache
sudo systemctl restart httpd
```

## Verification
We verified that Apache was running on port 6000 on all servers using `netstat`:

```bash
sudo netstat -tulpn | grep 6000
# Output should show 'httpd' listening on port 6000
```
