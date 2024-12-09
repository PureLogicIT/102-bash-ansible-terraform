# Installing Docker-ce using Ansible

## Verify Manual Install Steps

Take a look at the manual installation method and see the steps required each time docker needs to be installed.

[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

## Automated with Ansible

See how the requirements from the manual install have been recreated in ansible:


### Ensure the latest version of requirements is installed
```yml
    - name: Install required system packages
      apt:
        name:
          - ca-certificates
          - curl
        state: latest
        update_cache: true
```
### Add the docker gpg key
```yml
    - name: Add Docker GPG apt Key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present
```
### Add the Docker repository to apt sources
```yml
    - name: Add Docker Repository
      apt_repository:
        repo: deb https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable
        state: present
```
### Update and install docker-ce
```yml
    - name: Update apt and install docker-ce
      apt:
        name: docker-ce
        state: latest
        update_cache: true
```
## Install Docker-ce using Ansible

Run the following command to install docker-ce on your `HOST2` machine TODO

```bash
ansible-playbook -i inventory.yml docker-install.yml
```

