# Day 18: Configure LAMP server

## Challenge Description
xFusionCorp Industries is planning to host a WordPress website on their infra in Stratos Datacenter. We need to perform the following steps:
1. Install httpd, php and its dependencies on all app hosts.
2. Apache should serve on port 6400 within the apps.
3. Install/Configure MariaDB server on DB Server.
4. Create a database named `kodekloud_db2` and a user `kodekloud_sam` with password `8FmzjvFU6S`.
5. Ensure the user has full permissions on the created database.

## Key Concepts
*   LAMP Stack (Linux, Apache, MySQL/MariaDB, PHP)
*   Package Management (yum/dnf)
*   Apache Configuration (httpd.conf, Listen port)
*   Database Administration (Creating DB, Users, Grants)
*   Service Management (systemctl)

## Solution

### 1. App Servers (stapp01, stapp02, stapp03)
We performed the following steps on all three application servers.

**Login:**
*   `ssh tony@stapp01`
*   `ssh steve@stapp02`
*   `ssh banner@stapp03`

**Steps:**

1.  **Install Apache, PHP, and dependencies:**
    ```bash
    sudo yum install httpd php php-mysqli -y
    # Note: Depending on the OS version, the package might be php-mysqlnd or php-mysql
    ```

2.  **Configure Apache to listen on port 6400:**
    We modified the configuration file to change the default port 80 to 6400.
    ```bash
    sudo sed -i 's/Listen 80/Listen 6400/g' /etc/httpd/conf/httpd.conf
    ```

3.  **Start and Enable Apache:**
    ```bash
    sudo systemctl start httpd
    sudo systemctl enable httpd
    ```

### 2. Database Server (stdb01)

**Login:**
*   `ssh peter@stdb01`

**Steps:**

1.  **Install MariaDB Server:**
    ```bash
    sudo yum install mariadb-server -y
    ```

2.  **Start and Enable MariaDB:**
    ```bash
    sudo systemctl start mariadb
    sudo systemctl enable mariadb
    ```

3.  **Configure Database and User:**
    We accessed the MySQL shell and executed the necessary SQL commands.
    ```bash
    sudo mysql -u root
    ```
    ```sql
    CREATE DATABASE kodekloud_db2;
    CREATE USER 'kodekloud_sam'@'%' IDENTIFIED BY '8FmzjvFU6S';
    GRANT ALL PRIVILEGES ON kodekloud_db2.* TO 'kodekloud_sam'@'%';
    FLUSH PRIVILEGES;
    EXIT;
    ```

## Verification
To verify the setup:
1.  Check if `httpd` is running on app servers and listening on port 6400 (`netstat -tulnp | grep 6400`).
2.  Check if `mariadb` is running on the db server.
3.  Access the website via the LBR link (clicking "App" button) and confirm the connection message: "App is able to connect to the database using user kodekloud_sam".
