# Day 82: Create Ansible Inventory for App Server Testing

## Challenge Description
The Nautilus DevOps team is setting up Ansible for automation tasks. We need to create an Ansible inventory file on the **Jump Host** (`jump_host`) to facilitate running playbooks specifically against **App Server 3** (`stapp03`). The inventory must be in the INI format and include all necessary connection variables (user, password) so that playbooks can be executed without additional command-line arguments.

## Key Concepts
*   **Ansible Inventory:** A file (often in INI or YAML format) that lists the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate.
*   **Connection Variables:** Variables defined in the inventory (like `ansible_user`, `ansible_ssh_pass`) that tell Ansible how to connect to the remote hosts.
*   **Host Aliases:** Defining a short name (like `stapp03`) for a server and mapping it to its real IP address (`ansible_host`).

## Solution

### 1. Access the Jump Host
We logged into the jump host to perform the configuration:
*   **User:** `thor`
*   **Password:** `mjolnir123`

### 2. Create the Inventory File
We navigated to the playbook directory and created the `inventory` file:

```bash
cd /home/thor/playbook/
vi inventory
```

### 3. Configure App Server 3
We added the following line to the `inventory` file. This defines `stapp03` as the host and provides all credentials required for Ansible to connect and execute commands (including sudo privileges if needed).

```ini
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_become_pass=BigGr33n
```

*   `stapp03`: The inventory hostname (alias).
*   `ansible_host`: The actual IP address of App Server 3.
*   `ansible_user`: The SSH username (`banner`).
*   `ansible_ssh_pass`: The SSH password (`BigGr33n`).
*   `ansible_become_pass`: The password used for privilege escalation (sudo).

## Verification
To verify the connectivity, we ran an ad-hoc Ansible ping command using the newly created inventory:

```bash
ansible stapp03 -i inventory -m ping
```

The output confirmed a successful connection ("pong"), indicating that Ansible could successfully log in to App Server 3 using the provided credentials.
