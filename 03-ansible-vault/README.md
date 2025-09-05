# Ansible Vault

## What is an Ansible Vault?

Ansible Vault encrypts variables and files, allowing you to safeguard sensitive data like passwords or keys, instead of exposing them as plain text in playbooks or roles.

## Why use an Ansible Vault?

By using Ansible Vault, you ensure that sensitive information is safeguarded while maintaining the flexibility and efficiency of automation.

#### Use Case Examples

- Database Credentials: Securely store database passwords or connection strings.
- API Keys and Tokens: Encrypt API keys for third-party services.
- SSL/TLS Certificates: Safeguard private keys and certificates required for secure communication.
- SSH Keys: Store SSH private keys used for accessing servers.

## How to create an Ansible Vault

Before we create the vault we'll create a `group_vars` folder. Ansible automatically loads the files in this directory as variable files. The variables are assigned to hosts by group name.

```bash
mkdir -p group_vars/ubuntu
```

To create a new Ansible Vault file, use the `ansible-vault` command with the `create` option:

```bash
ansible-vault create group_vars/ubuntu/vault.yml
```

- You will be prompted to set a password to encrypt the newly created vault file. This password will be required to access or edit the vault file.
- Once the vault file is created, your default text editor (e.g., nano, vi) will open so that you can add details in YAML format.

```yaml
vault_ansibleuser_password: <PASSWORD>
```

- Save and close the vault file.


## How to decrypt an Ansible Vault

To decrypt an Ansible Vault file, use the `ansible-vault` command with the `decrypt` option:

```bash
ansible-vault decrypt group_vars/ubuntu/vault.yml
```

**Note:** The Ansible Vault file is converted back to plain text with the above decrypt command. **Use with caution**. Ensure that the file is re-encrypted once you have finished reviewing/modifying file. Re-encrypt the file with the following command:

```bash
ansible-vault encrypt file.txt
```


## How to edit an Ansible Vault

To edit an Ansible Vault file, use the `ansible-vault` command with the `edit` option:

```bash
ansible-vault edit group_vars/ubuntu/vault.yml
```
## How to use values in an Ansible Vault

Create a normal non-encrypted file that will call the encrypted vault variable. This gives us a single file to find all the variables later. While creating this file we'll also set the `ansible_user` which will set the username ansible uses

```bash
vi group_vars/ubuntu/vars
```

*group_vars/ubuntu/vars*
```ini
ansible_user: ansibleuser
ansibleuser_password: "{{vault_ansibleuser_password}}"
```

Modify the *inventory/hosts.yml* file to use the new vault password

```yaml
nginx:
  hosts:
    server1
ubuntu:
  hosts:
    server1:
      ansible_host: 172.31.7.123
      ansible_become_pass: {{ ansibleuser_password }}
    server2:
      ansible_host: 172.31.18.236
      ansible_become_pass: {{ ansibleuser_password }}
```

Now we can test it
```bash
ansible -m debug -a 'var=hostvars[inventory_hostname]' --ask-vault-password ubuntu
```

You should see the newley set variables in the output, including our vault values
```json
server1 | SUCCESS => {
    "hostvars[inventory_hostname]": {
        "ansible_become_pass": "ansibleuser",
        ...
        "ansible_host": "172.31.18.236",
        ...
        "ansible_user": "ansibleuser",
        ...
        "ansibleuser_password": "ansibleuser",
        ...
        "vault_ansibleuser_password": "ansibleuser"
    }
```

Writting the vault password each time can get tedious. For this course we will create a vault password file and use that.

```
vi .vault_password
```

Then edit the ansible.cfg and set the file

*ansible.cfg*
```ini
[defaults]
inventory = inventory
host_key_checking = False
vault_password_file = ./.vault_password
```

### Save to git
Time to save our progress!
```bash
git add .
git commit -m "Ansible vault"
git push

```
