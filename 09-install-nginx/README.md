# Install NGINX and upload new index.html file

We will be performing the same task we did in `101 - Linux Basics` but will be using Ansible to automate the tasks instead. Below are the modules we will use.

### Inventory items to apply the tasks
```yml
- name:
  hosts: nginx
  become: yes
```

### Apt Module to install nginx
```yml
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: latest
        update_cache: yes
```

### Systemd module for ensure the service has been started
```yml
    - name: Start nginx service
      ansible.builtin.systemd_service:
        name: nginx
        state: started
        enabled: true
```

### Copy module used for copying files using Ansible
```yml
    - name: Copy a new index.html file from Ansible client to target machine Nginx directory
      ansible.builtin.copy:
        src: index.html
        dest: /var/www/html/index.html
        owner: root
        group: root
```
### Save to git
Time to save our progress!
```bash
git add .
git commit -m "install nginx"
git push

```