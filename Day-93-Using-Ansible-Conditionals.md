# Day 93: Using Ansible Conditionals

## Challenge Description
The Nautilus DevOps team wants to utilize Ansible's conditionals to perform specific tasks on different application servers within the Stratos DC. The goal is to create a playbook that targets all hosts but uses `when` statements to copy specific files to specific servers based on their hostname.

Specifically:
- Copy `blog.txt` to `stapp01` (user: `tony`, permissions: `0655`).
- Copy `story.txt` to `stapp02` (user: `steve`, permissions: `0655`).
- Copy `media.txt` to `stapp03` (user: `banner`, permissions: `0655`).

All files should be copied from `/usr/src/devops` on the jump host to `/opt/devops` on the target servers.

## Key Concepts
- **Ansible Conditionals:** Using the `when` statement to execute tasks only if certain conditions are met.
- **Ansible Facts:** Utilizing gathered facts like `ansible_nodename` to identify target hosts.
- **Copy Module:** Managing file transfers, ownership, and permissions.
- **Privilege Escalation:** Using `become: yes` to perform tasks requiring root privileges (like writing to `/opt`).

## Solution
On the jump host, we created the playbook `/home/thor/ansible/playbook.yml` with the following content:

```yaml
---
- name: Copy files using conditionals
  hosts: all
  become: yes
  tasks:
    - name: Copy blog.txt to App Server 1
      copy:
        src: /usr/src/devops/blog.txt
        dest: /opt/devops/blog.txt
        owner: tony
        group: tony
        mode: '0655'
      when: ansible_nodename == "stapp01.stratos.xfusioncorp.com"

    - name: Copy story.txt to App Server 2
      copy:
        src: /usr/src/devops/story.txt
        dest: /opt/devops/story.txt
        owner: steve
        group: steve
        mode: '0655'
      when: ansible_nodename == "stapp02.stratos.xfusioncorp.com"

    - name: Copy media.txt to App Server 3
      copy:
        src: /usr/src/devops/media.txt
        dest: /opt/devops/media.txt
        owner: banner
        group: banner
        mode: '0655'
      when: ansible_nodename == "stapp03.stratos.xfusioncorp.com"
```

Then, we executed the playbook using the provided inventory:

```bash
ansible-playbook -i inventory playbook.yml
```

## Verification
To verify the changes, we can check the presence and properties of the files on each application server:

1. **For App Server 1:**
   ```bash
   ssh tony@stapp01 ls -l /opt/devops/blog.txt
   ```
   Output should show owner `tony`, group `tony`, and permissions `-rw-r-xr-x`.

2. **For App Server 2:**
   ```bash
   ssh steve@stapp02 ls -l /opt/devops/story.txt
   ```
   Output should show owner `steve`, group `steve`, and permissions `-rw-r-xr-x`.

3. **For App Server 3:**
   ```bash
   ssh banner@stapp03 ls -l /opt/devops/media.txt
   ```
   Output should show owner `banner`, group `banner`, and permissions `-rw-r-xr-x`.
