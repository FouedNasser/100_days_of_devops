# Day 30: Git Hard Reset

## Challenge Description
The development team needs to revert the git repository `/usr/src/kodekloudrepos/beta` on the storage server (`ststor01`) to a previous state. Specifically, we need to remove recent test commits and reset the `HEAD` and branch history back to the commit with the message "add data.txt file". This involves performing a hard reset and force-pushing the changes.

## Key Concepts
*   **Git Reset (`--hard`):** Moves the branch pointer to a specific commit and updates the working directory to match that commit, discarding all changes and commits after it.
*   **Git Log:** Used to view the commit history and identify the target commit hash.
*   **Git Push (`-f` / `--force`):** Overwrites the remote repository with the local history, which is necessary after rewriting history with a reset.

## Solution

1.  **Access the Storage Server**
    SSH into the storage server `ststor01` as user `natasha`.
    ```bash
    ssh natasha@ststor01
    # Password: Bl@kW
    ```

2.  **Navigate to the Repository**
    Move to the target repository directory.
    ```bash
    cd /usr/src/kodekloudrepos/beta
    ```

3.  **Identify the Target Commit**
    Check the git log to find the commit hash for "add data.txt file".
    ```bash
    git log --oneline
    ```
    We locate the commit hash (e.g., `abc1234`) associated with the message "add data.txt file".

4.  **Perform Hard Reset**
    Reset the repository to the identified commit. This removes all subsequent commits and changes from the working directory.
    ```bash
    git reset --hard <commit-hash>
    ```

5.  **Force Push Changes**
    Update the remote repository to reflect the reset history.
    ```bash
    git push -f origin master
    ```

## Verification
To verify, run `git log --oneline` again. You should see only the "initial commit" and the "add data.txt file" commit in the history.
