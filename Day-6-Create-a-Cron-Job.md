# Day 6: Create a Cron Job

## Challenge Description
The Nautilus system admins team has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in Stratos DC on a set schedule. Before that, they need to test similar functionality with a sample cron job. Therefore, we need to perform the following steps:
a. Install the `cronie` package on all Nautilus app servers and start the `crond` service.
b. Add a cron job `*/5 * * * * echo hello > /tmp/cron_text` for the `root` user.

## Key Concepts
*   **Cron:** A time-based job scheduler in Unix-like operating systems.
*   **Cronie:** A standard cron implementation for Linux systems.
*   **Crond service:** The daemon that runs scheduled cron jobs.
*   **Crontab:** The command-line utility used to create, view, and edit cron jobs.
*   **Cron job syntax:** `* * * * * command_to_execute` (minute, hour, day of month, month, day of week). `*/5` means "every 5 units" (e.g., every 5 minutes).
*   **`sudo`:** Used to execute commands with superuser privileges.

## Solution

We need to perform the following steps on all three app servers: `stapp01`, `stapp02`, and `stapp03`.

### General Steps for Each App Server

1.  **Connect to the server via SSH.**
2.  **Switch to the `root` user to perform administrative tasks.**
3.  **Install the `cronie` package, which provides the cron daemon.**
4.  **Start and enable the `crond` service to ensure it runs on boot.**
5.  **Add the cron job for the `root` user.**

### Server 1: `stapp01`

*   **Hostname:** `stapp01.stratos.xfusioncorp.com`
*   **IP Address:** `172.16.238.10`
*   **User:** `tony`
*   **Password:** `Ir0nM@n`

1.  **SSH into the server:**
    ```bash
    ssh tony@172.16.238.10
    ```

2.  **Switch to the root user:**
    ```bash
    sudo -i
    ```
    We will be prompted for `tony`'s password (`Ir0nM@n`).

3.  **Install the `cronie` package:**
    ```bash
    yum install -y cronie
    ```

4.  **Start and enable the `crond` service:**
    ```bash
    systemctl start crond
    systemctl enable crond
    ```

5.  **Add the cron job:**
    This command adds the job to the `root` user's crontab.
    ```bash
    echo "*/5 * * * * echo hello > /tmp/cron_text" | crontab -
    ```

### Server 2: `stapp02`

*   **Hostname:** `stapp02.stratos.xfusioncorp.com`
*   **IP Address:** `172.16.238.11`
*   **User:** `steve`
*   **Password:** `Am3ric@`

We repeat the same steps as above, but using the credentials for `stapp02`.

1.  **SSH into the server:**
    ```bash
    ssh steve@172.16.238.11
    ```

2.  **Switch to root, install, start/enable service, and add the cron job as before.**

### Server 3: `stapp03`

*   **Hostname:** `stapp03.stratos.xfusioncorp.com`
*   **IP Address:** `172.16.238.12`
*   **User:** `banner`
*   **Password:** `BigGr33n`

We repeat the same steps again, using the credentials for `stapp03`.

1.  **SSH into the server:**
    ```bash
    ssh banner@172.16.238.12
    ```

2.  **Switch to root, install, start/enable service, and add the cron job as before.**

## Verification

To verify the solution on each server:

1.  **Check if the `crond` service is running:**
    ```bash
    systemctl status crond
    ```
    We should see `Active: active (running)`.

2.  **Verify the cron job was added to the root user's crontab:**
    ```bash
    crontab -l
    ```
    We should see the output: `*/5 * * * * echo hello > /tmp/cron_text`

3.  **Wait for at least 5 minutes and then check the content of the `/tmp/cron_text` file:**
    ```bash
    cat /tmp/cron_text
    ```
    The file should contain the word `hello`.
