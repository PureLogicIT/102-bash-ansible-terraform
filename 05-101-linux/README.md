# Ansible Patching

For an excersise and reusability go back to the [101-linux-certificates-git](https://github.com/PureLogicIT/101-linux-certificates-git) git repo and recreate everything we did there but in Ansible.

High Level Tasks:
* Rename host to use the ansible's name has their hostname
* Patch the VMs
* Create the `courseadmin` group
* Give the group sudoers access to `apt-get`
* Create two new users (user1, user2)
* Add them to courseadmin group
* Add the CA certificate to the server
* Install NGINX on server1
* Install ScribbleRS on server1


Once done, don't forget to commit your changes to git
