# Day 91: Ansible Lineinfile Module

## Challenge Description
The Nautilus DevOps team want to install and set up a simple httpd web server on all app servers in Stratos DC. They also want to deploy a sample web page using Ansible. Therefore, write the required playbook to complete this task as per details mentioned below.

We already have an inventory file under `/home/thor/ansible` directory on jump host. Write a playbook `playbook.yml` under `/home/thor/ansible` directory on jump host itself. Using the playbook perform below given tasks:

*   Install `httpd` web server on all app servers, and make sure its service is up and running.
*   Create a file `/var/www/html/index.html` with content: `This is a Nautilus sample file, created using Ansible!`
*   Using `lineinfile` Ansible module add some more content in `/var/www/html/index.html` file. Below is the content: `Welcome to xFusionCorp Industries!`
*   Also make sure this new line is added at the top of the file.
*   The `/var/www/html/index.html` file's user and group owner should be `apache` on all app servers.
*   The `/var/www/html/index.html` file's permissions should be `0655` on all app servers.

## Key Concepts
*   **Ansible yum module:** Used to install the `httpd` package.
*   **Ansible service module:** Used to start and enable the `httpd` service.
*   **Ansible copy module:** Used to create the file with initial content and set initial permissions.
*   **Ansible lineinfile module:** Used to insert a specific line into a file.
    *   `insertbefore: BOF`: This parameter ensures the line is inserted at the "Beginning Of File".

## Solution
1.  We log in to the **Jump Server** as the `thor` user.
2.  We navigate to the Ansible directory:
    ```bash
    cd /home/thor/ansible
    ```
3.  We create the `playbook.yml` file with the following content:
    ```yaml
    - name: Configure Httpd and Index Page
      hosts: all
      become: yes
      tasks:
        - name: Install httpd
          yum:
            name: httpd
            state: present

        - name: Start and enable httpd service
          service:
            name: httpd
            state: started
            enabled: yes

        - name: Create index.html with initial content
          copy:
            dest: /var/www/html/index.html
            content: "This is a Nautilus sample file, created using Ansible!"
            owner: apache
            group: apache
            mode: '0655'

        - name: Add welcome message to the top of index.html
          lineinfile:
            path: /var/www/html/index.html
            line: "Welcome to xFusionCorp Industries!"
            insertbefore: BOF
            owner: apache
            group: apache
            mode: '0655'
    ```
4.  We execute the playbook:
    ```bash
    ansible-playbook -i inventory playbook.yml
    ```

## Verification
To verify the changes, we can run the following commands on the Jump Server (using `ansible` ad-hoc commands or ssh):

1.  **Check Service Status:**
    ```bash
    ansible all -i inventory -m shell -a "systemctl status httpd" -b
    ```
2.  **Verify File Content (Order matters):**
    ```bash
    ansible all -i inventory -m shell -a "cat /var/www/html/index.html" -b
    ```
    Output should look like:
    ```
    Welcome to xFusionCorp Industries!
    This is a Nautilus sample file, created using Ansible!
    ```
3.  **Verify Permissions and Ownership:**
    ```bash
    ansible all -i inventory -m shell -a "ls -l /var/www/html/index.html" -b
    ```
    Output should show permissions `-rw-r-xr-x` (which corresponds to 0655) and owner/group `apache`.
