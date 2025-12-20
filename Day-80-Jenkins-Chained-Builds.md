# Day 80: Jenkins Chained Builds

## Challenge Description
The DevOps team wants to automate the restart of the Apache service on all app servers only if the deployment on the storage server is successful. We need to implement a solution using Jenkins chained builds with two jobs:
1.  **nautilus-app-deployment:** An upstream job that pulls the latest changes from the git repository to the shared storage `/var/www/html`.
2.  **manage-services:** A downstream job that restarts the `httpd` service on all three application servers (`stapp01`, `stapp02`, `stapp03`), triggered only if the upstream job is stable.

## Key Concepts
*   **Jenkins Chained Builds:** Triggering a downstream job automatically upon the successful completion of an upstream job.
*   **Publish Over SSH Plugin:** A Jenkins plugin allowing execution of shell commands on remote servers via SSH.
*   **Sudo with Password (`sudo -S`):** Passing passwords to `sudo` non-interactively for privileged operations in automation scripts.

## Solution

### 1. Install "Publish Over SSH" Plugin
1.  We accessed the Jenkins Dashboard and went to **Manage Jenkins** -> **Manage Plugins**.
2.  In the **Available** tab, we searched for `Publish Over SSH`, selected it, and clicked **Install without restart**.
3.  We restarted Jenkins to ensure the plugin was loaded correctly.

### 2. Configure SSH Servers
1.  We navigated to **Manage Jenkins** -> **Configure System**.
2.  Under the **Publish over SSH** section, we added the following server configurations using **Add**:

    *   **Storage Server:**
        *   **Name:** `ststor01`
        *   **Hostname:** `172.16.238.15`
        *   **Username:** `natasha`
        *   **Remote Directory:** `/var/www/html`
        *   **Password:** `Bl@kW`

    *   **App Server 1:**
        *   **Name:** `stapp01`
        *   **Hostname:** `172.16.238.10`
        *   **Username:** `tony`
        *   **Password:** `Ir0nM@n`

    *   **App Server 2:**
        *   **Name:** `stapp02`
        *   **Hostname:** `172.16.238.11`
        *   **Username:** `steve`
        *   **Password:** `Am3ric@`

    *   **App Server 3:**
        *   **Name:** `stapp03`
        *   **Hostname:** `172.16.238.12`
        *   **Username:** `banner`
        *   **Password:** `BigGr33n`

    We clicked **Test Configuration** for each to ensure connectivity was successful.

### 3. Configure Upstream Job: `nautilus-app-deployment`
1.  We created a **Freestyle project** named `nautilus-app-deployment`.
2.  Under **Build Steps**, we selected **Send files or execute commands over SSH**.
3.  We configured the step as follows:
    *   **Name:** `ststor01`
    *   **Exec Command:**
        ```bash
        cd /var/www/html
        echo "Bl@kW" | sudo -S git config --global --add safe.directory /var/www/html
        echo "Bl@kW" | sudo -S git pull origin master
        ```
    *(Note: We used `sudo -S` to handle permissions for the shared directory and added the safe directory config to resolve git ownership errors.)*

### 4. Configure Downstream Job: `manage-services`
1.  We created a **Freestyle project** named `manage-services`.
2.  Under **Build Triggers**, we selected **Build after other projects are built**.
    *   **Projects to watch:** `nautilus-app-deployment`
    *   **Trigger only if build is stable:** Checked.
3.  Under **Build Steps**, we added three **Send files or execute commands over SSH** steps (or one step with multiple servers):

    *   **Server:** `stapp01`
        *   **Command:** `echo "Ir0nM@n" | sudo -S systemctl restart httpd`
    
    *   **Server:** `stapp02`
        *   **Command:** `echo "Am3ric@" | sudo -S systemctl restart httpd`
    
    *   **Server:** `stapp03`
        *   **Command:** `echo "BigGr33n" | sudo -S systemctl restart httpd`

## Verification
1.  We manually triggered the `nautilus-app-deployment` job by clicking **Build Now**.
2.  We verified the console output showed a successful git pull.
3.  We verified that `manage-services` was automatically triggered upon the completion of the upstream job.
4.  We checked the console output of the downstream job to confirm that the `httpd` service was restarted on all three app servers.
5.  Finally, we accessed the application via the Load Balancer URL to confirm the latest changes were visible.
