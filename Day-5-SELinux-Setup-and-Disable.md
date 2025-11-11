# Day 5: SELinux Setup and Disable

## Challenge Description
Following a security audit, the xFusionCorp Industries security team has opted to enhance application and server security with SELinux. To initiate testing, the following requirements have been established for App server 2 in the Stratos Datacenter:

*   Install the required SELinux packages.
*   Permanently disable SELinux for the time being; it will be re-enabled after necessary configuration changes.
*   No need to reboot the server, as a scheduled maintenance reboot is already planned for tonight.
*   Disregard the current status of SELinux via the command line; the final status after the reboot should be disabled.

## Key Concepts
*   **SELinux (Security-Enhanced Linux):** A security mechanism that provides mandatory access control (MAC) to the Linux kernel.
*   **`yum` or `dnf`:** Package managers used in CentOS/RHEL systems to install, update, and remove software packages.
*   **`setenforce`:** A command to change the SELinux mode at runtime (permissive, enforcing, disabled). This change is not persistent across reboots.
*   **`/etc/selinux/config`:** The main configuration file for SELinux, where the permanent SELinux mode is set.
*   **`SELINUX=disabled`:** The setting in the SELinux configuration file to permanently disable SELinux.

## Solution

To address the requirements, we will perform the following steps on `stapp02`:

1.  **SSH into `stapp02`:**
    ```bash
    ssh steve@stapp02
    ```

2.  **Install SELinux packages:**
    We will use `yum`, the default package manager for CentOS, to install a comprehensive set of SELinux packages. This ensures that the core policies, management utilities, and the main configuration file are all properly set up.
    *   `selinux-policy`: Provides the base SELinux policy.
    *   `selinux-policy-targeted`: Provides the targeted policy, which is the default for CentOS. This package includes the crucial `/etc/selinux/config` file.
    *   `policycoreutils`: Provides essential utilities for managing SELinux, such as `sestatus` and `setenforce`.
    ```bash
    sudo yum install -y selinux-policy selinux-policy-targeted policycoreutils
    ```

3.  **Permanently disable SELinux:**
    We will edit the SELinux configuration file `/etc/selinux/config` and set `SELINUX=disabled`.
    ```bash
    sudo sed -i 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
    ```
    Alternatively, we could use `vi` or `nano` to manually edit the file:
    ```bash
    sudo vi /etc/selinux/config
    # Change SELINUX=enforcing to SELINUX=disabled
    ```

## Verification

1.  **Verify the SELinux configuration file:**
    After making the change, we can verify that the `/etc/selinux/config` file has been updated correctly.
    ```bash
    ssh steve@stapp02 'cat /etc/selinux/config | grep SELINUX='
    ```
    The output should show `SELINUX=disabled`.

2.  **Check current SELinux status (optional, for understanding):**
    Although the challenge states to disregard the current status, we can check it to understand that the change will only take effect after a reboot.
    ```bash
    ssh steve@stapp02 'sestatus'
    ```
    This command will likely still show SELinux as `enforcing` or `permissive` until the server reboots. The key is that the configuration file is set to `disabled` for the next boot.
