# Installing Ansible
The recommanded way to install Ansible is with `pip` or `pipx`

1. Install pipx
   ```bash
   sudo apt update
   sudo apt install pipx
   pipx ensurepath
   ```

2. Install ansible
   ```bash
   pipx install --include-deps ansible
   ```

3. Logout and log back in to update the paths
