## Install HAProxy from source: Manual Installation
If you want to install HAProxy from source follow the instructions on `haproxy-installation` directory.

## Setup a HAProxy cluster with Keepalived: Ansible role
This project is used to set up a HAProxy cluster with Keepalived. It has a main playbook.yaml file which contains two roles as below:

`keepalived`

`haproxy`

This ansible playbook first install Keepalived and then install HAProxy on `Debian 10`. 

### Requirements
Install Ansible on your local machine and make sure you have root access on remote machines.

### Role Variables
At first before doing anything edit the `inventory` file based on your server information. After adding your server names change the name of every yaml file in `host_vars` directory based on your server names you've added in `inventory` file.

For installing Keepalived first change below variables:

Change `vip` in `group_vars/ha-cluster.yaml` based on your environment. This is the virtual IP address.

Then based on what your environment is going to be edit variables in yamle files in `host_vars` directory. Here is the definitions:

`role`: The role of that server in VRRP configuration. It could be `MASTER` or `BACKUP`.

`priority`: The higher priority is for server which has the role `MASTER`.

The variables for installing HAProxy are in `role/haproxy/defaults/main.yaml`. Here is what they are for:

`username`: The username for logging in HAProxy status pages.

`password`: The password for username which has been defined in previous variable.

`frontend_name`: The name of "fontend" configuration in HAProxy.

`listen_port`: The port which HAProxy is going to bind to. Default IP address is virtual IP  which has been defined in `group_vars/ha-cluster.yaml` and the default port is "80".

`mode`: It could be `tcp` or `http`.

`option`: "tcplog" or "httpclose"

`bakend_name`: The name of "backend" configuration.

`k8s_master_servers`: The dictionary which contains upstream server names and IP addresses. Because this HAProxy is going to be used in a k8s cluster the name of this variable starts with "k8s". Feel free to change it but remember to put new variable name in `roles/haproxy/templates/haproxy.cfg`. 
   
### Execution commands
After doing above steps execute below command:

`ansible-playbook -i inventory playbook.yaml`
