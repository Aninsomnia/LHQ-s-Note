### 配置yum源仓库

```shell
 sudo yum install -y yum-utils
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

### 配置国内docker镜像源

* ```
  vim /etc/docker/daemon.json 
  ```

  在其中添加：

  ```json
  {
  "registry-mirrors": ["https://registry.docker-cn.com","https://m8nvad2m.mirror.aliyuncs.com","http://hub-mirror.c.163.com"]
  }
  ```
  
  然后：
  
  ```
  systemctl daemon-reload
  ```
  
  
  
* Docker中国区官方镜像

  ```shell
  https://registry.docker-cn.com
  ```

  网易

  ```shell
  http://hub-mirror.c.163.com
  ```

  ustc 

  ```shell
  https://docker.mirrors.ustc.edu.cn
  ```

  我个人的阿里云加速器地址

  ```shell
  https://m8nvad2m.mirror.aliyuncs.com
  ```

### 安装Docker

* ```shell
  sudo yum install -y docker-ce docker-ce-cli containerd.io
  ```

* 如果出错：

  ```shell
  yum install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.5.10-3.1.el7.x86_64.rpm
  
  yum install -y docker-ce docker-ce-cli
  ```

* ```shell
  systemctl start docker
  ```

  

# Docker使用

* 删除所有容器：

  ```shell
  docker rm $(docker ps -aq)
  ```

  
