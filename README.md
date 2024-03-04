Below is a curated list of Ansible code examples, ranging from basic to more advanced use cases, which incorporate variables, loops, and other important Ansible features. These examples are designed to help you progressively learn Ansible, from understanding its basic syntax and operations to mastering more complex tasks and optimizations.

1. Basic Inventory and Ping
First, create an inventory file named hosts.ini and then execute a simple ping to check connectivity.

hosts.ini

```
[web]
server1.example.com

[db]
server2.example.com
```

Ping Command

```
ansible -i hosts.ini all -m ping
```

2. Simple Playbook to Update and Install Packages
A playbook to update package lists and install Vim on all web servers.

update_and_install.yml

```
- hosts: web
  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: yes
      become: true

    - name: Install Vim
      ansible.builtin.apt:
        name: vim
        state: present
      become: true
```

3. Using Variables
Use variables for more dynamic playbooks.

playbook_with_variables.yml

```
- hosts: "{{ target_hosts }}"
  vars:
    package_name: "nginx"
  tasks:
    - name: Install a package
      ansible.builtin.apt:
        name: "{{ package_name }}"
        state: present
      become: true
```

Execute with:

```
ansible-playbook -i hosts.ini playbook_with_variables.yml -e "target_hosts=web"
```

4. Looping with with_items
Install multiple packages using a loop.

install_multiple.yml

```
- hosts: all
  tasks:
    - name: Install multiple packages
      ansible.builtin.apt:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - tree
        - wget
      become: true
```


5. User Management with Variables and Loops
Create multiple users on a system.

create_users.yml

```
- hosts: all
  vars:
    users:
      - johndoe
      - janedoe
  tasks:
    - name: Ensure users are present
      ansible.builtin.user:
        name: "{{ item }}"
        state: present
        createhome: yes
      loop: "{{ users }}"
```

6. Conditional Execution
Run tasks based on conditions.

conditional_execution.yml

```
- hosts: all
  vars:
    install_nginx: true
  tasks:
    - name: Install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present
      when: install_nginx
      become: true
```

7. Templating with Jinja2
Generate a configuration file from a template.

templates/nginx.conf.j2

```
server {
    listen 80;
    server_name {{ server_name }};

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

nginx_template.yml

```
- hosts: web
  vars:
    server_name: "example.com"
  tasks:
    - name: Configure Nginx
      ansible.builtin.template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: restart nginx
  handlers:
    - name: restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

8. Roles for Organizing Playbooks
Use roles to organize tasks, handlers, files, templates, and variables.

Directory Structure

```
roles/
  common/
    tasks/
      main.yml
    handlers/
      main.yml
    files/
    templates/
    vars/
      main.yml
```

common/tasks/main.yml

```
- name: Update apt cache
  ansible.builtin.apt:
    update_cache: yes
  become: true
```

9. Using Ansible Vault for Sensitive Data
Store sensitive data like passwords securely.

Encrypt a variable file

```
ansible-vault create secret_vars.yml
```

Use in a playbook

```
- hosts: all
  vars_files:
    - secret_vars.yml
  tasks:
    - name: Print a secret
      debug:
        msg: "The secret is {{ secret_password }}"
```

Execute with:

```
ansible-playbook playbook.yml --ask-vault-pass
```

10. Advanced Loops with loop and include_tasks
Dynamically include tasks and loop over complex data structures.

Dynamic task inclusion

```
- hosts: all
  tasks:
    - name: Include task file for each item
      include_tasks: "{{ item }}"
      loop:
        - task_file_1.yml
        - task_file_2.yml
```

These examples cover a broad spectrum of Ansible's capabilities, from basic tasks and variable management to more sophisticated features like templating, roles, and secure variable management with Ansible Vault. As you grow more comfortable with these concepts, you can begin to explore even more advanced Ansible features and best practices.







