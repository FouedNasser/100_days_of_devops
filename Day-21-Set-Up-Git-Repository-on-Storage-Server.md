# Day 21: Set Up Git Repository on Storage Server

## Challenge Description
The Nautilus development team has provided requirements to the DevOps team for a new application development project, specifically requesting the establishment of a Git repository on the Storage Server (`ststor01`) in the Stratos DC.

The requirements are:

a. Utilize `yum` to install the `git` package on the Storage Server.
b. Create a bare repository named `/opt/apps.git` (ensure exact name usage).

## Key Concepts
*   **Git:** A distributed version control system.
*   **Bare Repository:** A Git repository that contains only the version control information (the contents of the `.git` directory) and no working tree (no checked-out files). It is typically used as a central repository for sharing and collaboration.
*   **`yum`:** The package manager used on the Storage Server (likely CentOS/RHEL 7 based).
*   **SSH:** Secure Shell for accessing remote servers.

## Solution

### 1. Connect to Storage Server
Login to the storage server using the provided credentials.

```bash
ssh natasha@ststor01
```
*Password: `Bl@kW`*

### 2. Install Git
Install the Git package using `yum`.

```bash
sudo yum install git -y
```

### 3. Create Bare Git Repository
Create the directory for the repository and initialize it as a bare Git repository.

1.  **Create the directory:**
    ```bash
    sudo mkdir -p /opt/apps.git
    ```

2.  **Initialize bare repository:**
    ```bash
    sudo git init --bare /opt/apps.git
    ```
    *Output should confirm initialization, e.g., `Initialized empty Git repository in /opt/apps.git/`*

## Verification
To verify the repository was created correctly:

1.  Check that the directory exists:
    ```bash
    ls -ld /opt/apps.git
    ```

2.  Check the contents to ensure it looks like a bare repo (contains `HEAD`, `config`, `refs`, `objects`, etc., but no source files):
    ```bash
    ls -F /opt/apps.git
    ```
