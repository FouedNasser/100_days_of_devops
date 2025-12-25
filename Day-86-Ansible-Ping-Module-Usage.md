# Day 86: Ansible Ping Module Usage

## Challenge Description
The Nautilus DevOps team needs to test Ansible playbooks on various application servers within the Stratos DC. A critical prerequisite is establishing a password-less SSH connection between the Ansible controller (Jump Host) and the managed nodes. 

For this task, we are required to:
- Use the Jump Host as the Ansible controller, operating as the `thor` user.
- Utilize the existing inventory file located at `/home/thor/ansible/inventory`.
- Set up password-less SSH for the `steve` user on App Server 2 (`stapp02`).
- Successfully test the Ansible `ping` module against App Server 2 from the Jump Host.

## Key Concepts
- **Ansible Inventory:** A configuration file that defines the hosts and groups of hosts upon which commands, modules, and playbooks operate.
- **Ansible Ping Module:** A simple test module that verifies a usable python is installed on the remote node and that we can connect to it.
- **SSH Key-Based Authentication:** A method that allows secure, password-less login between servers using a public and private key pair.
- **Ansible Connection Variables:** Parameters like `ansible_user` and `ansible_host` used within the inventory to define how Ansible connects to managed nodes.

## Solution

1. **SSH into the Jump Host:**
   We start by logging into the Jump Host as the `thor` user.

2. **Generate SSH Key Pair:**
   On the Jump Host, we generate an RSA key pair if one doesn't already exist.
   ```bash
   ssh-keygen -t rsa -b 2048
   ```

3. **Establish Password-less SSH to App Server 2:**
   We copy the public key to the `steve` user on `stapp02` to enable password-less authentication.
   ```bash
   ssh-copy-id steve@stapp02
   ```
   *(We enter the password `Am3ric@` when prompted)*.

4. **Update the Inventory File:**
   We verify the content of the inventory file at `/home/thor/ansible/inventory`. Since we are connecting to `stapp02` as the `steve` user, we must ensure the inventory specifies the correct user.
   ```bash
   # Before update
   # stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@

   # Update the inventory to include the ansible_user
   sed -i 's/stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@/stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve/' /home/thor/ansible/inventory
   ```

5. **Test Connectivity with Ansible Ping:**
   Finally, we run the Ansible ping module using the specified inventory file.
   ```bash
   cd /home/thor/ansible
   ansible -i inventory stapp02 -m ping
   ```

## Verification
A successful connection will return a `SUCCESS` message and a `"ping": "pong"` response:

```json
stapp02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
