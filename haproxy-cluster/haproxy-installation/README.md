## This document shows how to install haproxy from source with prometheus exporter
~~~~
curl -LO http://www.haproxy.org/download/2.2/src/haproxy-2.2.9.tar.gz
tar xf haproxy-2.2.9.tar.gz
cd haproxy-2.2.9
make TARGET=linux-glibc USE_PCRE=1 USE_OPENSSL=1 USE_ZLIB=1 USE_CRYPT_H=1 USE_LIBCRYPT=1 USE_SYSTEMD=1 EXTRA_OBJS="contrib/prometheus-exporter/service-prometheus.o"
sudo make install
~~~~

Now create a serivce file for haproxy.
~~~~
vim /etc/systemd/system/haproxy.service

    [Unit]
    Description=HAProxy Load Balancer
    After=syslog.target network.target
    
    [Service]
    Environment="CONFIG=/etc/haproxy/haproxy.cfg" "PIDFILE=/run/haproxy.pid"
    ExecStartPre=/usr/sbin/haproxy -f $CONFIG -c -q
    ExecStart=/usr/sbin/haproxy -Ws -f $CONFIG -p $PIDFILE $OPTIONS
    ExecReload=/usr/sbin/haproxy -f $CONFIG -c -q
    ExecReload=/bin/kill -USR2 $MAINPID
    KillMode=mixed
    Restart=always
    Type=notify
    
    [Install]
    WantedBy=multi-user.target
~~~~

Now start haproxy service
~~~~
sudo systemctl daemon-reload; sudo systemctl enable haproxy; sudo systemctl start haproxy
~~~~
