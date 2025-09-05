# Ansible Patching

We'll create our first 'playbook'

***first-playbook.yaml***
```yaml
---
- hosts: all
  tasks:
    - name: echo
      command: echo "First task"
```

Run the playbook.

```bash
ansible-playbook first-playbook.yaml
```

So we can see that it ran and did something, but not what it actually did. There a few options to see that. The first would be to use verbose mode `-v`.

```bash
ansible-playbook -v first-playbook.yaml
```

The other way is to use the `debug` task. Before we can use that we need to capture the output of the first command


***first-playbook.yaml***
```yaml
---
- hosts: all
  tasks:
    - name: echo
      command: echo "First task"
      register: echo

    - name: debug var echo
      debug:
       var: echo

    - name: debug msg echo
      debug:
        msg: "{{echo}}"
```

This playbook is very static and won't be too useful in a normal environment. We'll add variables to make more dynamic.
```

***first-playbook.yaml***
```yaml
---
- hosts: all
  vars:
    msg: "variable"
  tasks:
    - name: echo
      command: 'echo "{{ msg }}"'
```

Ansible has the concept of list, we can loop through items of the list. In older versions of ansible you will see this referenced has `with_items`, that is still backwards compatible but will be depricated eventually.

***first-playbook.yaml***
```yaml
---
- hosts: all
  vars:
    msg:
      - one
      - two
      - three
  tasks:
    - name: echo with var
      command: 'echo "{{ item }}"'
      loop: "{{ msg }}"

    - name: echo with list
      command: 'echo "{{ item }}"'
      loop:
        - four
        - five
        - six
```

This playbook works, but isn't "best pratice" for the current versions of ansible. We're refering to the modules only by their names and not their collection. By default ansible will assume you are refering to the `ansible.builtin` collection but if a local module has the same name it will be used and could cause issues. So rewritting the entire playbook with the collection names will look like this.

***first-playbook.yaml***
```
- hosts: all
  vars:
    msg:
      - one
      - two
      - three
  tasks:
    - name: echo with var
      ansible.builtin.command:
        cmd: 'echo "{{ item }}"'
      register: echo
      loop: "{{ msg }}"

    - name: debug var echo
      ansible.builtin.debug:
       var: echo

    - name: echo with list
      ansible.builtin.command: 'echo "{{ item }}"'
      register: echo
      loop:
        - four
        - five
        - six

    - name: debug var echo
      ansible.builtin.debug:
       var: echo
```

Lastly we'll move the variable to the inventory file which makes more sense as a global variable.

***inventory/hosts.yaml***
```yaml
nginx:
  hosts:
    server1
ubuntu:
  hosts:
    server1:
      ansible_host: 172.16.1.91
      msg:
        - one
        - two
        - three
    server2:
      ansible_host: 172.16.1.78
      msg:
        - four
        - five
        - six
  vars:
   ansible_user: ansibleuser   
```

***first-playbook.yaml***
```
- hosts: all
  tasks:
    - name: echo with var
      ansible.builtin.command:
        cmd: 'echo "{{ item }}"'
      register: echo
      loop: "{{ msg }}"

    - name: debug var echo
      ansible.builtin.debug:
       var: echo

```


