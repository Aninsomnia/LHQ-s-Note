etcd官方中文文档：https://doczhcn.gitbook.io/etcd/

# 二进制包安装

* 3.5.1版本：

  ```shell
  ETCD_VER=v3.5.1
  
  # choose either URL
  GOOGLE_URL=https://storage.googleapis.com/etcd
  GITHUB_URL=https://github.com/etcd-io/etcd/releases/download
  DOWNLOAD_URL=${GOOGLE_URL}
  
  rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
  mkdir -p /tmp/etcd-download-test
  
  curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
  tar xzvf /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz -C /tmp/etcd-download-test --strip-components=1
  rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
  ```

* 查看版本：

  ```shell
  /tmp/etcd-download-test/etcd --version
  /tmp/etcd-download-test/etcdctl version
  /tmp/etcd-download-test/etcdutl version
  ```

* 启动etcd：

  ```shell
  # start a local etcd server
  /tmp/etcd-download-test/etcd
  ```

* 查看成员：

  ```shell
  ETCDCTL_API=3 /tmp/etcd-download-test/etcdctl member list
  ```
  
  ```shell
  ETCDCTL_API=3 /tmp/etcd-download-test/etcdctl -w table --endpoints=192.168.153.130:2379,192.168.153.131:2379 endpoint status
  ```
  
* 删除成员：

  ```shell
  ETCDCTL_API=3 /tmp/etcd-download-test/etcdctl member remove 
  ```
  
* 添加成员：

  ```shell
  ETCDCTL_API=3 /tmp/etcd-download-test/etcdctl member add EtcdNode_2 --peer-urls=http://192.168.153.131:2380
  ```
  
* 读写操作：

  ```shell
  # write,read to etcd
  /tmp/etcd-download-test/etcdctl --endpoints=localhost:2379 put foo bar
  /tmp/etcd-download-test/etcdctl --endpoints=localhost:2379 get foo
  ```

* 卸载：

  ```shell
  rm -rf /tmp/etcd-download-test
  ```


# 通过编译源码安装

* 拉取源码：

  ```shell
  cd /root
  
  git clone -b v3.5.0 https://github.com/etcd-io/etcd.git
  ```

* 编译安装：

  ```shell
  cd etcd
  
  ./build.sh
  ```

# etcd使用

### 查看

* 查看成员：

  ```shell
  ETCDCTL_API=3 etcdctl member list
  ```


### 启动集群

* 启动前，关闭**防火墙**、**iptables**和**SELINUX**：

  ```shell
  #查看防火墙状态
  firewall-cmd --state
  
  #停止firewall
  systemctl stop firewalld.service
  
  #禁止firewall开机启动
  systemctl disable firewalld.service</pre>
  
  #关闭SELINUX
  #编辑SELINUX文件
  vim /etc/selinux/config
  
  #将SELINUX=enforcing改为SELINUX=disabled
  #清除和关闭iptables
  #清空iptables规则
  iptables -F
  
  #保存
  iptables-save
  ```

* 启动：

  ```shell
  /tmp/etcd-download-test/etcd --name EtcdNode_1 --initial-advertise-peer-urls http://192.168.153.130:2380 \
    --listen-peer-urls http://192.168.153.130:2380 \
    --listen-client-urls http://192.168.153.130:2379,http://127.0.0.1:2379 \
    --advertise-client-urls http://192.168.153.130:2379 \
    --initial-cluster EtcdNode_1=http://192.168.153.130:2380,EtcdNode_2=http://192.168.153.131:2380 \
    --initial-cluster-state new
  ```

  ```shell
  /tmp/etcd-download-test/etcd --name EtcdNode_2 --initial-advertise-peer-urls http://192.168.153.131:2380 \
    --listen-peer-urls http://192.168.153.131:2380 \
    --listen-client-urls http://192.168.153.131:2379,http://127.0.0.1:2379 \
    --advertise-client-urls http://192.168.153.131:2379 \
    --initial-cluster EtcdNode_1=http://192.168.153.130:2380,EtcdNode_2=http://192.168.153.131:2380 \
    --initial-cluster-state new
  ```

# 创建etcd.service

* 创建配置文件：

  ```shell
  mkdir /etc/etcd
  chmod 777 /etc/etcd
  touch /etc/systemd/system/etcd.service
  chmod 777 /etc/systemd/system/etcd.service
  ```

  

  ```shell
  vim /etc/systemd/system/etcd.service
  ```

  ```shell
  [Unit]
  Description=Etcd Server
  After=network.target
  After=network-online.target
  Wants=network-online.target
  
  [Service]
  Type=notify
  WorkingDirectory=/tmp/etcd-download-test/
  ExecStart=/bin/bash -c "/tmp/etcd-download-test/etcd --config-file=/etc/etcd/conf.yml"
  [Install]
  WantedBy=multi-user.target
  ```

  ```shell
  systemctl daemon-reload
  systemctl start etcd.service
  
  systemctl status etcd
  ```

  ```shell
  vi /etc/etcd/conf.yml
  
  cat > /etc/etcd/conf.yml <<EOF
  name: EtcdNode_1
  data-dir: /tmp/etcd-download-test/data
  listen-client-urls: http://192.168.153.130:2379,http://127.0.0.1:2379
  advertise-client-urls: http://192.168.153.130:2379
  listen-peer-urls: http://192.168.153.130:2380
  initial-advertise-peer-urls: http://192.168.153.130:2380
  initial-cluster: EtcdNode_1=http://192.168.153.130:2380,EtcdNode_2=http://192.168.153.131:2380
  initial-cluster-state: new
  EOF
  ```
  
  ```shell
  cat > /etc/etcd/conf.yml <<EOF
  name: EtcdNode_2
  data-dir: /tmp/etcd-download-test/data
  listen-client-urls: http://192.168.153.131:2379,http://127.0.0.1:2379
  advertise-client-urls: http://192.168.153.131:2379
  listen-peer-urls: http://192.168.153.131:2380
  initial-advertise-peer-urls: http://192.168.153.131:2380
  initial-cluster: EtcdNode_1=http://192.168.153.130:2380,EtcdNode_2=http://192.168.153.131:2380
  initial-cluster-state: new
  EOF
  ```
  
  ```
  rm -rf /tmp/etcd-download-test/data && /tmp/etcd-download-test/etcd --config-file=/etc/etcd/conf.yml
  ```
  
  
