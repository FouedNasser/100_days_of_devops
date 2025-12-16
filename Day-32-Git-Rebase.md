# Day 32: Git Rebase

## Challenge Description
The development team needs to update the `feature` branch of the `/usr/src/kodekloudrepos` repository on the storage server (`ststor01`) with the latest changes from the `master` branch. The requirement is to use `git rebase` instead of `git merge` to maintain a linear history and avoid a merge commit. We then need to push the updated `feature` branch.

## Key Concepts
*   **Git Rebase:** Moves or combines a sequence of commits to a new base commit. It effectively takes the unique commits from the current branch and reapplies them on top of another branch (e.g., `master`).
*   **Linear History:** Rebasing avoids the "diamond" shape in git history caused by merge commits, making the log easier to read.
*   **Force Push:** Required after rebasing published commits because the history has been rewritten.

## Solution

1.  **Access the Storage Server**
    SSH into the storage server `ststor01` using user `natasha`.
    ```bash
    ssh natasha@ststor01
    # Password: Bl@kW
    ```

2.  **Navigate to the Repository**
    Move to the repository directory.
    ```bash
    cd /usr/src/kodekloudrepos
    ```

3.  **Update Master Branch**
    Ensure the local `master` branch is up-to-date with the remote.
    ```bash
    git checkout master
    git pull origin master
    ```

4.  **Rebase Feature Branch**
    Switch to the `feature` branch and rebase it onto `master`.
    ```bash
    git checkout feature
    git rebase master
    ```
    *Note: If there were conflicts, we would resolve them, `git add` the files, and run `git rebase --continue`.*

5.  **Push Changes**
    Force push the rebased `feature` branch to the remote repository.
    ```bash
    git push -f origin feature
    ```

## Verification
We can verify the rebase by checking the git log on the `feature` branch. The commits from `master` should appear before the `feature` branch commits, and there should be no merge commit.
```bash
git log --oneline --graph --all
```
