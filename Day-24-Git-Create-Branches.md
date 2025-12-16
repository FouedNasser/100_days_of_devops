# Day 24: Git Create Branches

## Challenge Description
On Storage server in Stratos DC create a new branch `xfusioncorp_beta` from the `master` branch in the `/usr/src/kodekloudrepos/beta` git repository.

## Key Concepts
*   **Git Branching:** Git branches are essentially pointers to a snapshot of our changes. When we want to add a new feature or fix a bug, we create a new branch to isolate our changes from the main codebase.
*   **`git branch` command:** Used to create, list, or delete branches.
*   **`git checkout` command:** Used to switch branches or restore working tree files.

## Solution
To create a new branch named `xfusioncorp_beta` from the `master` branch on the Storage server (`ststor01`), we follow these steps:

1.  **SSH into the Storage Server (`ststor01`):**
    We use the `ssh` command to connect to `ststor01` as the user `natasha`.
    ```bash
    ssh natasha@ststor01
    ```

2.  **Navigate to the Git Repository:**
    Once connected, we navigate to the specified Git repository directory.
    ```bash
    cd /usr/src/kodekloudrepos/beta
    ```

3.  **Checkout the `master` branch:**
    It's good practice to ensure we are on the `master` branch before creating a new branch from it.
    ```bash
    git checkout master
    ```

4.  **Create the new branch `xfusioncorp_beta`:**
    We use the `git branch` command to create the new branch.
    ```bash
    git branch xfusioncorp_beta
    ```

## Verification
To verify that the new branch `xfusioncorp_beta` has been created, we can list all branches using the `git branch` command:
```bash
git branch
```
The output should show `xfusioncorp_beta` listed among the existing branches, with an asterisk indicating the currently active branch.
