# Nginx configuration: Ansible role
This project is used to install and configure Nginx web server. This project works for both Debian and RedHat distributions.

## Requirements
Install Ansible on your local machine and make sure you have root access on remote machines.

## Role Variables
Add new domain name in "roles/vhost-configuration/defaults/main.yaml"

`server_name: NEW_DOMAIN_NAME`
   
## Execution commands
For installing Nginx you can run below command:

`ansible-playbook -i inventory playbook.yaml --tags installation`

For adding a new vhost configuration without SSL sertificate run below command:

`ansible-playbook -i inventory playbook.yaml --tags configuration`

