# Day 83: Troubleshoot and Create Ansible Playbook

## Challenge Description

The task for Day 83 was to troubleshoot and complete an Ansible setup on the jump host. The existing inventory file was pointing to the wrong application server and was missing authentication details. We needed to correct the inventory to target App Server 3, create a new Ansible playbook to perform a task on it, and ensure the playbook could run non-interactively.

## Key Concepts

-   **Ansible Inventory:** A file (usually in INI or YAML format) that defines the hosts and groups of hosts upon which Ansible commands, modules, and tasks operate.
-   **Ansible Playbook:** A YAML file containing one or more plays, which define a set of tasks to be executed on managed nodes.
-   **Ansible `file` module:** A module used to manage files and their properties, such as creating, deleting, or changing attributes of files, symlinks, and directories.
-   **Password-based Authentication:** While not the most secure method, Ansible can use password-based SSH authentication by providing credentials in the inventory, often requiring the `sshpass` utility. For this, we use the `ansible_ssh_pass` variable.

## Solution

1.  **Log into the Jump Host**

    We first logged into the jump host as the `thor` user to access the Ansible configuration files.

    ```bash
    ssh thor@jump_host
    ```

2.  **Update the Inventory File**

    The inventory file at `/home/thor/ansible/inventory` was incorrect. We updated it to target `stapp03` (`172.16.238.12`) with the user `banner` and added the necessary password for SSH authentication.

    The final content of the `/home/thor/ansible/inventory` file was:

    ```ini
    stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass='BigGr33n' ansible_ssh_common_args='-o StrictHostKeyChecking=no'
    ```

3.  **Create the Ansible Playbook**

    Next, we created the playbook file `/home/thor/ansible/playbook.yml`. This playbook contains a single task to create an empty file at `/tmp/file.txt` on the target host.

    ```yaml
    - name: Troubleshoot and Create Ansible Playbook
      hosts: all
      tasks:
        - name: Create an empty file /tmp/file.txt
          file:
            path: /tmp/file.txt
            state: touch
    ```

4.  **Execute the Playbook**

    With the inventory and playbook correctly configured, we executed the playbook.

    ```bash
    ansible-playbook -i inventory playbook.yml
    ```

## Verification

To confirm the playbook ran successfully, we connected to App Server 3 and verified the existence of the newly created file.

```bash
ssh banner@stapp03 "ls /tmp/file.txt"
```

The command returned the path `/tmp/file.txt`, confirming the task was completed.
