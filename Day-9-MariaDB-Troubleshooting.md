# Day 9: MariaDB Troubleshooting

## Challenge Description

There is a critical issue going on with the Nautilus application in Stratos DC. The production support team identified that the application is unable to connect to the database. After digging into the issue, the team found that mariadb service is down on the database server.

Look into the issue and fix the same.

## Key Concepts

*   **MariaDB:** An open-source relational database management system, a community-developed fork of MySQL.
*   **`systemctl`:** A command-line utility for controlling the `systemd` system and service manager. Used to start, stop, enable, and check the status of services.
*   **`/var/lib/mysql`:** The default data directory for MariaDB, where all databases, tables, and other data are stored.
*   **`chown`:** A command used to change the owner and group of files and directories. Essential for ensuring the `mysql` user has the necessary permissions to access its data directory.
*   **Database Initialization:** The process where the database system sets up its internal data structures and system tables in its data directory. If the data directory is empty or improperly configured, this process may fail.

## Solution

The MariaDB service was down because its data directory (`/var/lib/mysql`) was either missing or had incorrect permissions, preventing the `mariadb-prepare-db-dir` pre-startup script from initializing or accessing it correctly. The `journalctl` logs showed an error indicating that the directory was not empty when initialization was attempted, or that it was not properly set up for the `mysql` user.

To fix this, we performed the following steps on the database server (`stdb01`):

1.  **SSH into the database server:**
    We logged in as `peter` to the database server.
    ```bash
    ssh peter@stdb01
    ```
    (Password: `Sp!dy`)

2.  **Verify the issue:**
    We confirmed the MariaDB service status.
    ```bash
    sudo systemctl status mariadb
    ```
    And checked the logs for errors.
    ```bash
    sudo journalctl -xeu mariadb.service
    ```
    The logs indicated an issue with the `/var/lib/mysql` directory not being properly initialized or accessible.

3.  **Create the MariaDB data directory (if it was missing):**
    We ensured the data directory existed.
    ```bash
    sudo mkdir -p /var/lib/mysql
    ```
    *(Note: In this specific scenario, the directory `/var/lib/mysqld` existed but was not correctly linked or used by the systemd service as `/var/lib/mysql`. The actual fix involved ensuring the `/var/lib/mysql` directory itself was correctly present and owned by the `mysql` user.)*

4.  **Change ownership of the data directory:**
    We set the correct owner and group for the `/var/lib/mysql` directory to `mysql:mysql`, allowing the MariaDB server to read and write to it.
    ```bash
    sudo chown -R mysql:mysql /var/lib/mysql
    ```

5.  **Start the MariaDB service:**
    After ensuring the directory was correctly set up, we started the MariaDB service.
    ```bash
    sudo systemctl start mariadb
    ```

## Verification

We verified that the MariaDB service was running successfully.

1.  **Check MariaDB service status:**
    ```bash
    sudo systemctl status mariadb
    ```
    The output should show `Active: active (running)`.

    **Example of successful output:**
    ```
    ● mariadb.service - MariaDB 10.5 database server
         Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; preset: disabled)
         Active: active (running) since Thu 2025-12-11 14:30:00 UTC; 1min ago
         ...
    ```