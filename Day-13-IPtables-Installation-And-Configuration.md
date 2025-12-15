# Day 13: IPtables Installation And Configuration

## Challenge Description
We have one of our websites up and running on our Nautilus infrastructure in Stratos DC. Our security team has raised a concern that right now Apache’s port i.e 8087 is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:

1. Install iptables and all its dependencies on each app host.
2. Block incoming port 8087 on all apps for everyone except for LBR host.
3. Make sure the rules remain, even after system reboot.

## Key Concepts
*   **IPtables:** A user-space utility program that allows a system administrator to configure the IP packet filter rules of the Linux kernel firewall.
*   **Chains:** Lists of rules (e.g., INPUT, OUTPUT, FORWARD) that packets traverse.
*   **Persistence:** Ensuring firewall rules survive a system reboot using `iptables-services`.

## Solution

We performed the following steps on all App Servers (`stapp01`, `stapp02`, and `stapp03`).

### 1. Install and Configure IPtables Service
First, we installed the necessary packages to manage iptables and ensure persistence.

```bash
# Connect to the App Server (example for stapp01)
ssh tony@stapp01

# Install iptables-services
sudo yum install iptables-services -y

# Start and enable the service
sudo systemctl start iptables
sudo systemctl enable iptables
```

### 2. Configure Firewall Rules
We needed to allow traffic from the Load Balancer (`172.16.238.14`) on port 8087 and drop traffic from all other sources on that same port. We inserted the rules at specific positions in the INPUT chain to ensure they were processed in the correct order (Allow first, then Drop).

```bash
# Allow traffic from LBR (172.16.238.14) on port 8087 (Inserted at line 5)
sudo iptables -I INPUT 5 -p tcp -s 172.16.238.14 --dport 8087 -j ACCEPT

# Drop all other traffic on port 8087 (Inserted at line 6)
sudo iptables -I INPUT 6 -p tcp --dport 8087 -j DROP
```

### 3. Persist Changes
To ensure the rules remain after a reboot, we saved the current configuration.

```bash
sudo service iptables save
```

## Verification
We verified the rules were applied correctly by listing them:

```bash
sudo iptables -L -n -v --line-numbers
```

The output confirmed that the ACCEPT rule for the LBR IP on port 8087 precedes the DROP rule for the same port.
