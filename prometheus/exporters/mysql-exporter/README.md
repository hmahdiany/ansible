## Install Mysql exporter
Mysql exporter is an exporter which is used to gather database statistics and collect them in Prometheus.

# Download and extract the latest version of mysql exporter
~~~~
curl -s https://api.github.com/repos/prometheus/mysqld_exporter/releases/latest   | grep browser_download_url   | grep linux-amd64 | cut -d '"' -f 4   | wget -qi -
tar xf mysqld_exporter*.tar.gz
~~~~

# Move binary file to /usr/local/bin
~~~~
mv  mysqld_exporter-*.linux-amd64/mysqld_exporter /usr/local/bin/
chmod +x /usr/local/bin/mysqld_exporter
~~~~

# create prometheus exporter database user
~~~~
CREATE USER 'mysqld_exporter'@'<DatabaseHostIP>' IDENTIFIED BY 'StrongPassword' WITH MAX_USER_CONNECTIONS 2;
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'mysqld_exporter'@'<DatabaseHostIP>';
FLUSH PRIVILEGES;
EXIT
~~~~

# Configure Database credential
Create  `/etc/.mysqld_exporter.cnf` and add below line in it. Edit the value of user and password based on real information you have entered in previous steps.
~~~~
[client]
user=mysqld_exporter
password=StrongPassword
host=DatabaseHostIP
~~~~

After creating file change the ownership
~~~~
chown root:node_exporter /etc/.mysqld_exporter.cnf
~~~~

Then create service file for Mysql exporter. Note that here we create the service with `node_exporter` user. You can use another user but do not forget to set correct ownership in previous step.
~~~~
[Unit]
 Description=Prometheus MySQL Exporter
 After=network.target
 User=node_exporter
 Group=node_exporter

 [Service]
 Type=simple
 Restart=always
 ExecStart=/usr/local/bin/mysqld_exporter \
 --config.my-cnf /etc/.mysqld_exporter.cnf \
 --collect.global_status \
 --collect.info_schema.innodb_metrics \
 --collect.auto_increment.columns \
 --collect.info_schema.processlist \
 --collect.binlog_size \
 --collect.info_schema.tablestats \
 --collect.global_variables \
 --collect.info_schema.query_response_time \
 --collect.info_schema.userstats \
 --collect.info_schema.tables \
 --collect.perf_schema.tablelocks \
 --collect.perf_schema.file_events \
 --collect.perf_schema.eventswaits \
 --collect.perf_schema.indexiowaits \
 --collect.perf_schema.tableiowaits \
 --collect.slave_status \
 --web.listen-address=0.0.0.0:9104

 [Install]
 WantedBy=multi-user.target
~~~~

 # Enable and start Mysql service
~~~~
systemctl daemon-reload; systemctl enable mysql_exporter; systemctl start mysql_exporter
~~~~

 # Sample configuration for prometheus.yml
~~~~
- job_name: 'mysql_server'
    scrape_interval: 5s
    static_configs:
    - targets: ['<DatabaseHostIP>:9104']
~~~~

