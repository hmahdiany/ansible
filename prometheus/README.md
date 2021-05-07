# Install Prometheus, Alertmanager and Grafana
This ansible project is used to install a monitoring platform based on Prometheus, Alertmanager and Grafana in a single Debian server. There are three main roles in 'playbook.yaml': `prometheus`, `alertmanager` and `grafana` which each of them install one module. 

## Requirements
Install Ansible on your local machine and make sure you have root access on remote machines.

## Role Variables
At first before doing anything edit the `inventory` file based on your server information.

For installing Prometheus first we should edit variables in `roles/prometheus/defaults/main.yaml`.

`server_name`: This is the Prometheus url in Nginx configuration and will be  replaced in `roles/prometheus/templates/prometheus.conf`

For installing Alertmanager add correct values in `roles/alertmanager/defaults/main.yaml'. Here are definitions:

`latest_version`: Download link of the latest version of Alertmanager

`package_name`: The actual package name which will be downloaded for example: `alertmanager-0.21.0.linux-amd64`

`alertmanager_url`: Alertmanager url in Nginx configuration. It will be added in `roles/alertmanager/templates/alertmanager.conf`

 
