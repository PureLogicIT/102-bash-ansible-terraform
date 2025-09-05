# Ansible Patching

The code block below will check for pending updates, elevate to root to perform updates if there are any available, and then check if a reboot flag exists to then reboot if necessary. This example is targeting a Ubuntu host. 

```yaml
---
- hosts: all
  serial: 2 # Serial is used to control how many hosts are managed at a time, in this case 2 at a time. Without specifying this, the default will run against all hosts in parallel.
  tasks:
    - name: Checking for pending updates
      command: /usr/lib/update-notifier/apt-check --package-names # This command checks for packages that have available updates and then lists the names, and registers those package names to the varialbe "packages"
      register: packages
      changed_when: packages.stderr != "" # This will only result in a "changed" status reported by ansible when the output from stderr is not blank, so there will be no changes reported if there are no new package updates available

    - name: update, upgrade, autoremove
      become: yes # Become root user for the task
      apt: 
        update_cache: yes # This is the equivalent of apt-get update, which gets info about latest package versions and dependencies
        upgrade: full # This is the equivalent of running apt-get upgrade. The other options here are no, dist, and safe.
        autoremove: yes # This removes unused dependency pacakges
      when: packages.stderr != "" # This line sets the condition on when to run this task, in this case it only runs if there were no errors reported in the "Checking for pending updates" task

    - name: Check if a reboot is required
      stat:
        path: /var/run/reboot-required # This file only exists in Ubuntu when a reboot is required
      register: reboot_required
      changed_when: reboot_required.stat.exists

    - name: Reboot system if required
      become: yes
      reboot:
        reboot_timeout: 900 # This is the maximum amount of seconds to wait for a reboot to happen and have the system respond to a test command
      when: reboot_required.stat.exists == true # This task will only happen based on the registered variable "reboot_required" result from the last task
      register: restart_status
```

### Save to git
Time to save our progress!
```bash
git add .
git commit -m "patching"
git push

```