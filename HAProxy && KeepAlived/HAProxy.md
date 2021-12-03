# 安装

* ```shell
  yum install haproxy psmisc -y
  ```

* 配置文件：

  ```shell
  vim /etc/haproxy/haproxy.cfg
  ```

  如果要配置Kubernetes的高可用：

  ```shell
  frontend kube-apiserver
      bind *:6443
      mode tcp
      option tcplog
      default_backend kube-apiserver
  
  backend kube-apiserver
      option httpchk GET /healthz
      http-check expect status 200
      mode tcp
      option ssl-hello-chk
      balance     roundrobin
      default-server inter 10s downinter 5s rise 2 fall 2 slowstart 60s maxconn 250 maxqueue 256 weight 100
      server kube-apiserver-1 192.168.153.130:6443 check
      server kube-apiserver-2 192.168.153.131:6443 check
  
  ```
  



# 启动

* 需要提前运行：

  ```
  setsebool -P haproxy_connect_any=1
  ```

* ```shell
  systemctl enable haproxy
  systemctl restart haproxy
  ```

  