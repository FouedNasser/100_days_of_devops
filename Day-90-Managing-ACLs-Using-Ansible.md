# Day 90: Managing ACLs Using Ansible

## Challenge Description
There are some files that need to be created on all app servers in Stratos DC. The Nautilus DevOps team want these files to be owned by user root only however, they also want that the app specific user to have a set of permissions on these files. All tasks must be done using Ansible only, so they need to create a playbook. Below you can find more information about the task.

Create a playbook named `playbook.yml` under `/home/thor/ansible` directory on jump host, an inventory file is already present under `/home/thor/ansible` directory on Jump Server itself.

*   Create an empty file `blog.txt` under `/opt/security/` directory on app server 1. Set some acl properties for this file. Using acl provide read `(r)` permissions to group `tony` (i.e entity is `tony` and etype is `group`).
*   Create an empty file `story.txt` under `/opt/security/` directory on app server 2. Set some acl properties for this file. Using acl provide read + write `(rw)` permissions to user `steve` (i.e entity is `steve` and etype is `user`).
*   Create an empty file `media.txt` under `/opt/security/` on app server 3. Set some acl properties for this file. Using acl provide read + write `(rw)` permissions to group `banner` (i.e entity is `banner` and etype is `group`).

## Key Concepts
*   **Ansible acl Module:** Used to set and retrieve ACL (Access Control List) information for files and directories.
*   **Ansible file Module:** Used to manage files and file properties (creation, deletion, ownership, permissions).
*   **Inventory Groups:** Organizing hosts into groups allows targeting specific tasks to specific servers.
*   **Become:** Running tasks with root privileges to manage system files and ACLs.

## Solution
1.  We log in to the **Jump Server** as the `thor` user.
2.  We navigate to the Ansible directory:
    ```bash
    cd /home/thor/ansible
    ```
3.  We create the `playbook.yml` file with tasks targeted at specific app servers:
    ```yaml
    - name: Manage ACLs on App Servers
      hosts: all
      become: yes
      tasks:
        - name: Ensure /opt/security directory exists
          file:
            path: /opt/security
            state: directory

        - name: Setup for App Server 1
          block:
            - name: Create blog.txt on App Server 1
              file:
                path: /opt/security/blog.txt
                state: touch
                owner: root
                group: root

            - name: Set ACL for group tony on blog.txt
              acl:
                path: /opt/security/blog.txt
                entity: tony
                etype: group
                permissions: r
                state: present
          when: inventory_hostname == 'stapp01'

        - name: Setup for App Server 2
          block:
            - name: Create story.txt on App Server 2
              file:
                path: /opt/security/story.txt
                state: touch
                owner: root
                group: root

            - name: Set ACL for user steve on story.txt
              acl:
                path: /opt/security/story.txt
                entity: steve
                etype: user
                permissions: rw
                state: present
          when: inventory_hostname == 'stapp02'

        - name: Setup for App Server 3
          block:
            - name: Create media.txt on App Server 3
              file:
                path: /opt/security/media.txt
                state: touch
                owner: root
                group: root

            - name: Set ACL for group banner on media.txt
              acl:
                path: /opt/security/media.txt
                entity: banner
                etype: group
                permissions: rw
                state: present
          when: inventory_hostname == 'stapp03'
    ```
4.  We run the playbook using the local inventory:
    ```bash
    ansible-playbook -i inventory playbook.yml
    ```

## Verification
To verify the ACL settings, we use the `getfacl` command on each server:

*   **App Server 1:**
    ```bash
    ssh tony@stapp01 "getfacl /opt/security/blog.txt"
    ```
    We should see: `group:tony:r--`
*   **App Server 2:**
    ```bash
    ssh steve@stapp02 "getfacl /opt/security/story.txt"
    ```
    We should see: `user:steve:rw-`
*   **App Server 3:**
    ```bash
    ssh banner@stapp03 "getfacl /opt/security/media.txt"
    ```
    We should see: `group:banner:rw-`
