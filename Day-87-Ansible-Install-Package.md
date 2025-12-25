# Day 87: Ansible Install Package

## Challenge Description
The Nautilus Application development team requires the `httpd` package to be installed on all application servers in the Stratos Datacenter. We are tasked with automating this installation using Ansible.

The requirements are:
1. Create an inventory file at `/home/thor/playbook/inventory` containing all app servers.
2. Create an Ansible playbook at `/home/thor/playbook/playbook.yml` to install the `httpd` package using the `yum` module.
3. Ensure the playbook can be executed by the `thor` user on the jump host without extra command-line arguments.

## Key Concepts
- **Ansible Yum Module:** Used to manage packages on systems that use the YUM package manager (like CentOS/RHEL).
- **Ansible Inventory:** Defines the managed nodes and their specific connection parameters (IP, user, password).
- **Privilege Escalation (`become`):** Allows Ansible to execute tasks with root privileges, which is necessary for package installation.
- **Ansible Playbook:** A YAML file containing a series of tasks to be executed on managed nodes.

## Solution

1. **Create the Inventory File:**
   We defined the three application servers in `/home/thor/playbook/inventory`, specifying the connection host, user, and password for each.
   ```ini
   stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_become_pass=Ir0nM@n
   stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_become_pass=Am3ric@
   stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_become_pass=BigGr33n
   ```

2. **Create the Playbook:**
   We created the playbook `/home/thor/playbook/playbook.yml` to target all hosts and ensure the package is present.
   ```yaml
   ---
   - name: install packages
     hosts: all
     become: yes
     tasks:
       - name: install httpd
         yum:
           name: httpd
           state: present
   ```

3. **Run the Playbook:**
   We executed the playbook from the jump host using the specified inventory.
   ```bash
   cd /home/thor/playbook
   ansible-playbook -i inventory playbook.yml
   ```

## Verification
Upon successful execution, the Ansible output shows the tasks completing for all three servers:

```text
PLAY [install packages] *****************************************************************

TASK [Gathering Facts] ******************************************************************
ok: [stapp01]
ok: [stapp02]
ok: [stapp03]

TASK [install httpd] ********************************************************************
changed: [stapp01]
ok: [stapp02]
changed: [stapp03]

PLAY RECAP ******************************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
*(Note: `stapp02` might show `ok` if the package was already installed during a previous attempt).*
