# Day 69: Install Jenkins Plugins

The Nautilus DevOps team has recently set up a Jenkins server and needs to install specific plugins to enable integration with version control systems like Git and GitLab.

## Challenge Description

*   Log in to the Jenkins UI using the provided credentials.
*   Install the **Git** and **GitLab** plugins.
*   Restart Jenkins if necessary to complete the installation.
*   Verify that the plugins are installed and active.

## Key Concepts

*   **Jenkins Plugins:** Extensions that provide additional functionality to Jenkins, such as integration with external tools.
*   **Plugin Manager:** The interface within Jenkins used to find, install, and manage plugins.
*   **Update Center:** The backend service that provides plugin metadata and downloads.
*   **Restarting Jenkins:** Some plugins require a restart of the Jenkins service to be fully initialized.

## Solution

### 1. Access and Login to Jenkins

We click on the **Jenkins** button on the top bar to access the web UI. We log in using the following credentials:

*   **Username:** `admin`
*   **Password:** `Adm!n321`

### 2. Navigate to Plugin Management

Once logged in, we perform the following steps:
1.  Click on **Manage Jenkins** in the left-hand sidebar.
2.  Select **Plugins** from the configuration section.

### 3. Install Git and GitLab Plugins

1.  Click on the **Available plugins** tab.
2.  In the search box, we search for `Git`.
3.  We check the box next to the **Git** plugin.
4.  Next, we search for `GitLab` in the search box.
5.  We check the box next to the **GitLab** plugin.
6.  Click on **Install without restart** (or **Download now and install after restart**).

### 4. Restart Jenkins

On the installation progress page, we check the option:
*   **Restart Jenkins when installation is complete and no jobs are running**.

We then wait for the installation to finish and for the Jenkins service to restart. Once the login page reappears, the process is complete.

## Verification

To verify the installation:
1.  Log back into Jenkins.
2.  Go to **Manage Jenkins** > **Plugins** > **Installed plugins**.
3.  Search for "Git" and "GitLab" to ensure they are listed and enabled.
