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

  
