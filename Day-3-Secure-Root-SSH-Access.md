# Day 3: Secure Root SSH Access

## Challenge Description

The task is to disable direct SSH root login on all app servers (`stapp01`, `stapp02`, `stapp03`) in the Stratos Datacenter, as per new security protocols.

## Key Concepts

*   **SSH (Secure Shell):** A cryptographic network protocol for operating network services securely over an unsecured network.
*   **sshd_config:** The server-side configuration file for SSH, typically located at `/etc/ssh/sshd_config`.
*   **PermitRootLogin:** A directive in `sshd_config` that controls whether the root user can log in using SSH.

## Solution

To disable root login, we need to edit the `sshd_config` file on each app server and then restart the SSH service.

### Steps for each server:

1.  **Connect to the server:**
    We will SSH into each app server from the jump host. For example, to connect to `stapp01`:

    ```bash
    ssh tony@stapp01
    ```
    *(Password: `Ir0nM@n`)*

2.  **Gain root privileges:**
    We use `sudo` to run commands with root privileges.

    ```bash
    sudo -i
    ```

3.  **Modify the SSH configuration:**
    We will open the `/etc/ssh/sshd_config` file for editing.

    ```bash
    vi /etc/ssh/sshd_config
    ```
    Inside the file, we need to find the `PermitRootLogin` directive and set its value to `no`. If it's commented out, we must uncomment it.

    ```
    PermitRootLogin no
    ```

4.  **Restart the SSH service:**
    To apply the changes, we restart the `sshd` service.

    ```bash
    systemctl restart sshd
    ```

5.  **Exit the server:**

    ```bash
    exit
    exit
    ```

These steps must be repeated for all three app servers:
*   `stapp01` (user: `tony`, password: `Ir0nM@n`)
*   `stapp02` (user: `steve`, password: `Am3ric@`)
*   `stapp03` (user: `banner`, password: `BigGr33n`)

## Verification

To verify the solution, we can try to SSH directly as the root user to one of the app servers. The connection should be refused.

From the jump host:
```bash
ssh root@stapp01
```

This command should result in a "Permission denied" error, confirming that direct root login is disabled.
