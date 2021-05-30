# Install Prometheus, Alertmanager and Grafana
This ansible project is used to install a monitoring platform based on Prometheus, Alertmanager and Grafana in a single Debian server. There are four main roles in 'playbook.yaml': `firewall`, `prometheus`, `alertmanager` and `grafana` which each of them install one module. 

## Requirements
Install Ansible on your local machine and make sure you have root access on remote machines.

## Role Variables
At first before doing anything edit the `inventory` file based on your server information.

Variables in `roles/alertmanager/defaults/main.yaml`

* `alertmanager_version`: Desire version you want to install.
* `os_arch`: Operating system architecture you want to install. Used for downloading the correct package.
* `alertmanager_url`: Used to configure Nginx. You can access to alertmanager web panel via this name so it should be a valid DNS name.
* `slack_webhook_url`: (optional) Add correct slack webhook url if you want to send alerts to your slack channel.

Variables in `roles/prometheus/defaults/main.yaml`.

* `prometheus_version`: Desirable Prometheus version you want to install.
* `nodeexporter_version`: Desirable Node Exporter version you want to install.
* `os_arch`: Operating system architecture you want to install. Used for downloading the correct package.
* `retention_time`: Number of days you want Prometheus to keep history. Used to configure `--storage.tsdb.retention.time` in `role/prometheus/templates/prometheus.service`. Default value is `20d`.
* `retention_size`: Maximum disk which Prometheus can use to keep history. Default value is `40GB`.
* `prometheus_url`: Used to configure Nginx. You can access to prometheus web panel via this name so it should be a valid DNS name.
* `targets`: (optional) If you know the targets at the time of installation you can add them to configure `role/prometheus/templates/prometheus.yml`


### Important note:
This playbook configure UFW to only open below ports and deny any other incoming traffic So take care about your other firewall configuration.

* 22
* 80
* 443

### Authenticaion
When Ansible has done its jobs if you want to enable basic authentication for Prometheus and Alertmanager web panel, you can do below steps:
Install `apache2-utils`:
~~~~
sudo apt install apache2-utils
~~~~

Create a file which contains the username and a hash password. Here the user name is `admin`:
~~~~
sudo htpasswd -c /etc/nginx/.htpasswd admin
~~~~

Now change Nginx configuration file to enable basic authentication. Here is a sample configuration:
~~~~
server {
  listen 80;
  server_name prometheus.mentorship.local;

  access_log /var/log/nginx/prometheus-access.log;
  error_log /var/log/nginx/prometheus-error.log;

  location / {
    auth_basic  "Need Authentication";
    auth_basic_user_file /etc/nginx/.htpasswd;

    proxy_pass http://localhost:9090;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
~~~~
