# Day 71: Configure Jenkins Job for Package Installation

## Challenge Description
The goal of this challenge is to create a Jenkins job named `install-packages` to automate the installation of packages on the storage server (`ststor01`). The job should be parameterized to accept the package name as input.

## Key Concepts
*   **Jenkins:** An open-source automation server.
*   **Parameterized Build:** A Jenkins feature that allows passing variables to the build process.
*   **SSH (Secure Shell):** A protocol for operating network services securely over an unsecured network.
*   **sshpass:** A utility to pass the ssh password non-interactively.
*   **Sudo non-interactive:** Using `sudo -S` to read the password from standard input.

## Solution

1.  **Access Jenkins:**
    *   We accessed the Jenkins UI and logged in with the credentials: `admin` / `Adm!n321`.

2.  **Install Plugins (if necessary):**
    *   We ensured that the SSH plugin was installed. (Manage Jenkins > Manage Plugins).

3.  **Create the Job:**
    *   We created a new **Freestyle project** named `install-packages`.

4.  **Configure Parameters:**
    *   In the **General** section, we checked **This project is parameterized**.
    *   We added a **String Parameter** named `PACKAGE`.

5.  **Configure Build Step:**
    *   In the **Build** section, we added an **Execute shell** step.
    *   We used the following command to connect to the storage server (`ststor01`) as user `natasha` and install the package using `sudo`. We used `sshpass` to provide the SSH password and piped the sudo password to `sudo -S` to run it non-interactively.

    ```bash
    sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@ststor01 "echo 'Bl@kW' | sudo -S yum install -y $PACKAGE"
    ```

6.  **Save:**
    *   We saved the job configuration.

## Verification

1.  **Run the Job:**
    *   We clicked on **Build with Parameters**.
    *   We entered a package name (e.g., `git`) in the `PACKAGE` field.
    *   We clicked **Build**.

2.  **Check Output:**
    *   We opened the **Console Output** of the build.
    *   We verified that the build finished with status `SUCCESS` and the package installation logs indicated a successful installation on `ststor01`.
