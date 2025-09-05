# Understanding Roles and Ansible Galaxy
## What are Roles in Ansible?

Ansible roles are a way to organize and modularize your configuration and automation tasks. They allow you to break down your playbooks into reusable and structured components. Each role encapsulates a specific functionality, making it easier to manage and reuse tasks, variables, files, and templates across multiple playbooks or projects.

Roles are stored in directories with a standardized structure, which Ansible automatically recognizes and uses during execution.

## How are Roles used in Ansible?

Roles are typically used in playbooks. Instead of defining all tasks, variables, and files directly in the playbook, you can include roles to simplify your playbooks.

Here’s how you would use a role in an Ansible playbook:

```yaml
- hosts: webservers
  roles:
    - apache
```

In this example `apache` role will execute all tasks, use any variables, and apply configurations files defined in its directory.

### Role Directory Structure

A typical Ansible role has the following structure:

```python
roles/
  apache/
    tasks/
      main.yml       # Main task file
    handlers/
      main.yml       # Handlers for service notifications
    templates/
      apache.conf.j2 # Jinja2 templates
    files/
      index.html     # Static files
    vars/
      main.yml       # Role-specific variables
    defaults/
      main.yml       # Default variables
    meta/
      main.yml       # Role metadata
```

### Example Roles

**Example Role 1: Installing and configuring Apache**

This Role installs the Apache web server, configures it with a template file, and starts the service.

> **Directory Structure:**

```css
roles/
  apache/
    tasks/
      main.yml
    templates/
      apache.conf.j2
```

> tasks/main.yml:

```yaml
- name: Install Apache
  ansible.builtin.yum:
    name: httpd
    state: present

- name: Deploy Apache Configuration
  ansible.builtin.template:
    src: apache.conf.j2
    dest: /etc/httpd/conf/httpd.conf

- name: Start Apache Service
  ansible.builtin.service:
    name: httpd
    state: started
    enabled: true
```

**Example Role2: Configuring a Firewall**

This role configures firewall rules for a web server.

> **Directory Structure:**

```css
roles/
  firewall/
    tasks/
      main.yml
```

tasks/main.yml

```yaml
- name: Allow HTTP Traffic
  ansible.builtin.firewalld:
    port: 80/tcp
    state: enabled
    permanent: true

- name: Reload Firewall Rules
  ansible.builtin.service:
    name: firewalld
    state: reloaded
```


## Ansible Galaxy

**Ansible Galaxy** is a platform for sharing, discovering, and downloading Ansible roles and collections. It simplifies the process of finding reusable roles created by the Ansible community or official contributors. Instead of writing every role from scratch, you can search for pre-written roles on Ansible Galaxy that meet your needs, saving time and effort.

> **Using Ansible Galaxy**

> 1. **Search for Roles:** You can search for roles on the [Ansible Galaxy](https://galaxy.ansible.com/) website.
> 2. **Install a Role:** Use the `ansible-galaxy` command to download and install a role. For example:
```bash
ansible-galaxy install geerlingguy.apache
```
> 3. **Use the Installed Role:** Include the role in your playbook just like any other role. 

**Example Workflow**

```bash
# Install a role from Galaxy
ansible-galaxy install geerlingguy.apache

# Include the role in your playbook
- hosts: all
  roles:
    - geerlingguy.apache
``` 

### Save to git
Time to save our progress!
```bash
git add .
git commit -m "ansible galaxies and roles"
git push

```