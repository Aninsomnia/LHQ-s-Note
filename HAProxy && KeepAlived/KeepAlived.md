# 安装

* ```shell
  yum install keepalived -y
  ```

* 配置文件：

  ```shell
  vim /etc/keepalived/keepalived.conf
  ```

  LB-1：

  ```
  global_defs {
    notification_email {
    }
    router_id LVS_DEVEL
    vrrp_skip_check_adv_addr
    vrrp_garp_interval 0
    vrrp_gna_interval 0
  }
     
  vrrp_script chk_haproxy {
    script "/etc/keepalived/check_apiserver.sh"
    interval 2
    weight 2
  }
     
  vrrp_instance haproxy-vip {
    state MASTER
    priority 101
    interface ens33                       # Network card
    virtual_router_id 60
    advert_int 1
    authentication {
      auth_type PASS
      auth_pass 1111
    }
    unicast_src_ip 192.168.153.132      # The IP address of this machine
    unicast_peer {
      192.168.153.133                         # The IP address of peer machines
    }
     
    virtual_ipaddress {
      192.168.153.10                 # The VIP address
    }
     
    track_script {
      chk_haproxy
    }
  }
  ```

  LB-2:

  ```
  global_defs {
    notification_email {
    }
    router_id LVS_DEVEL
    vrrp_skip_check_adv_addr
    vrrp_garp_interval 0
    vrrp_gna_interval 0
  }
     
  vrrp_script chk_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
  }
     
  vrrp_instance haproxy-vip {
    state BACKUP
    priority 100
    interface ens33                       # Network card
    virtual_router_id 60
    advert_int 1
    authentication {
      auth_type PASS
      auth_pass 1111
    }
    unicast_src_ip 192.168.153.133      # The IP address of this machine
    unicast_peer {
      192.168.153.132                         # The IP address of peer machines
    }
     
    virtual_ipaddress {
      192.168.153.10                  # The VIP address
    }
     
    track_script {
      chk_haproxy
    }
  }
  ```



* ```
  vim /etc/keepalived/check_apiserver.sh
  ```

  

# 启动

* ```
  
  
  systemctl enable keepalived
  systemctl restart keepalived
  ```
  
* 运行以下命令查看是否成功

  ```shell
  ip a s
  ```

  