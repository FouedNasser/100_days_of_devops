# Day 74: Jenkins Database Backup Job

The DevOps team requires an automated solution to back up the application database periodically. We have been tasked with creating a Jenkins job that performs a database dump and transfers it to a secure backup server.

## Challenge Description

*   Create a Jenkins job named `database-backup`.
*   The job must take a dump of the `kodekloud_db01` database located on the Database server (`stdb01`).
*   Database credentials: User `kodekloud_roy`, Password `asdfgdsd`.
*   The dump file must be named `db_$(date +%F).sql`.
*   The dump file must be copied to the Backup Server (`stbkp01`) under `/home/clint/db_backups`.
*   The job must be scheduled to run every 10 minutes using the cron expression `*/10 * * * *`.

## Key Concepts

*   **Jenkins Automation:** Using Jenkins to schedule and execute repetitive tasks.
*   **mysqldump:** A utility to create logical backups of MariaDB/MySQL databases.
*   **Publish Over SSH Plugin:** A Jenkins plugin used to transfer files and execute commands on remote servers via SSH.
*   **Cron Scheduling:** Defining time-based execution patterns for automated jobs.
*   **Remote Execution:** Using `sshpass` and `ssh` to run commands on remote servers from the Jenkins workspace.

## Solution

### 1. Install Necessary Plugins

1.  We logged into the Jenkins UI (`admin` / `Adm!n321`).
2.  We navigated to **Manage Jenkins** > **Manage Plugins** > **Available**.
3.  We searched for and installed the **Publish Over SSH** plugin, then restarted Jenkins if prompted.

### 2. Configure SSH Server for Backup

1.  We went to **Manage Jenkins** > **Configure System**.
2.  In the **Publish over SSH** section, we added a new SSH Server:
    *   **Name:** `BackupServer`
    *   **Hostname:** `stbkp01`
    *   **Username:** `clint`
    *   **Remote Directory:** `/`
3.  Under **Advanced**, we checked **Use password authentication** and set the password to `H@wk3y3`.
4.  We tested the configuration to ensure it worked and saved the settings.

### 3. Create and Configure the Jenkins Job

1.  We created a new **Freestyle project** named `database-backup`.
2.  In the **Build Triggers** section, we checked **Build periodically** and entered `*/10 * * * *`.
3.  In the **Build** section, we added an **Execute shell** step with the following logic:

    ```bash
    # Variables for DB server and credentials
    DB_SERVER="stdb01"
    DB_USER="kodekloud_roy"
    DB_PASS="asdfgdsd"
    SYS_USER="peter"
    SYS_PASS="Sp!dy"
    DUMP_FILE="db_$(date +%F).sql"

    # Remove old dumps from workspace
    rm -f db_*.sql

    # Execute mysqldump on stdb01 via SSH and redirect output to a local file
    sshpass -p "$SYS_PASS" ssh -o StrictHostKeyChecking=no $SYS_USER@$DB_SERVER "mysqldump -u $DB_USER -p$DB_PASS kodekloud_db01" > "$DUMP_FILE"
    ```

4.  In the **Post-build Actions** section, we added **Send build artifacts over SSH**:
    *   **SSH Server:** `BackupServer`
    *   **Source files:** `db_*.sql`
    *   **Remote directory:** `/home/clint/db_backups/`
5.  We saved the job.

## Verification

1.  **Manual Build:** We clicked **Build Now** to trigger the job manually.
2.  **Console Output:** We verified the console log showed the `mysqldump` command succeeded and reported `Transferred 1 file(s)`.
3.  **Remote Verification:** We checked the backup server to confirm the file existed:
    ```bash
    ssh clint@stbkp01 "ls -l /home/clint/db_backups/"
    ```
4.  **Schedule:** We monitored the Jenkins dashboard to confirm the job triggered automatically every 10 minutes.
