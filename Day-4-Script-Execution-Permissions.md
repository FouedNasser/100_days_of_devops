# Day 4: Script Execution Permissions

## Challenge Description:
In a bid to automate backup processes, the xFusionCorp Industries sysadmin team has developed a new bash script named xfusioncorp.sh. While the script has been distributed to all necessary servers, it lacks executable permissions on App Server 2 within the Stratos Datacenter.

Our task is to grant executable permissions to the `/tmp/xfusioncorp.sh` script on App Server 2. Additionally, we need to ensure that all users have the capability to execute it.

## Key Concepts:
*   **`chmod`**: This command is used to change file permissions in Unix-like operating systems.
*   **Permissions (rwx)**:
    *   `r` (read): Allows viewing the file's content.
    *   `w` (write): Allows modifying the file's content.
    *   `x` (execute): Allows running the file as a program or script.
*   **Octal Mode**: Uses a three-digit number (e.g., `755`) where each digit represents permissions for user, group, and others, respectively.
    *   `7` (binary `111`) = read, write, execute
    *   `5` (binary `101`) = read, execute

## Solution:
To grant executable permissions to the `/tmp/xfusioncorp.sh` script on App Server 2 for all users, we will follow these steps:

1.  **Connect to App Server 2**:
    We need to establish an SSH connection to `stapp02.stratos.xfusioncorp.com` using the `steve` user.
    ```bash
    ssh steve@stapp02.stratos.xfusioncorp.com
    ```

2.  **Grant executable permissions**:
    Once connected to App Server 2, we will use the `chmod` command with octal mode `755`. This sets permissions as `rwxr-xr-x`, meaning:
    *   Owner: read, write, execute
    *   Group: read, execute
    *   Others: read, execute
    This ensures all users can execute the script, while also providing appropriate read/write control for the owner.

    ```bash
    chmod 755 /tmp/xfusioncorp.sh
    ```

## Verification:
To verify that the permissions have been correctly applied, we can check the file's permissions using the `ls -l` command on App Server 2.

1.  **Check file permissions**:
    After connecting to App Server 2, run the following command:
    ```bash
    ls -l /tmp/xfusioncorp.sh
    ```
    The output should show permissions similar to `-rwxr-xr-x`, indicating read, write, and execute for the owner, and read and execute for the group and others.
