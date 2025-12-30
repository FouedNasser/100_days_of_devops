# Managing Jinja2 Templates Using Ansible

## Challenge Description
The Nautilus DevOps team requires a Jinja2 template for the `index.html` file as part of an `httpd` role. This template must dynamically display the hostname of the server where it is deployed. Additionally, the role needs to be applied to App Server 2, and the deployed file must have specific permissions and ownership.

## Key Concepts
- **Ansible Roles**: A way to group tasks, templates, and variables into reusable units.
- **Jinja2 Templates**: A templating engine for Python used by Ansible to generate dynamic files.
- **`inventory_hostname`**: A magic variable in Ansible that contains the name of the current host as defined in the inventory.
- **Ansible `template` Module**: Used to process a Jinja2 template and copy it to the remote server.
- **File Permissions and Ownership**: Managing access rights and owners using `mode`, `owner`, and `group` parameters.

## Solution

### 1. Create the Jinja2 Template
We create the template file at `/home/thor/ansible/role/httpd/templates/index.html.j2` with the dynamic content:

```bash
vi /home/thor/ansible/role/httpd/templates/index.html.j2
```
Content:
```jinja2
This file was created using Ansible on {{ inventory_hostname }}
```

### 2. Update the Role Tasks
We add the task to deploy the template in `/home/thor/ansible/role/httpd/tasks/main.yml`. We use `{{ ansible_user }}` to ensure the owner and group match the sudo user used to connect to the server.

```bash
vi /home/thor/ansible/role/httpd/tasks/main.yml
```
Content:
```yaml
---
# tasks file for httpd
- name: Install httpd package
  yum:
    name: httpd
    state: latest

- name: Start and enable httpd service
  service:
    name: httpd
    state: started
    enabled: yes

- name: Copy index.html from template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    mode: '0655'
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
```

### 3. Update the Playbook
We update the main playbook `~/ansible/playbook.yml` to target `stapp02` and include the `httpd` role.

```bash
vi ~/ansible/playbook.yml
```
Content:
```yaml
---
- hosts: stapp02
  become: yes
  roles:
    - role/httpd
```

### 4. Run the Playbook
We execute the playbook using the inventory file on the jump host:

```bash
ansible-playbook -i inventory playbook.yml
```

## Verification
To verify the deployment, we check the content and permissions of the `index.html` file on App Server 2:

```bash
ssh steve@stapp02 "ls -l /var/www/html/index.html"
ssh steve@stapp02 "cat /var/www/html/index.html"
```
The output should show:
- Permissions: `-rw-r-xr-x` (0655)
- Owner/Group: `steve`
- Content: `This file was created using Ansible on stapp02`
