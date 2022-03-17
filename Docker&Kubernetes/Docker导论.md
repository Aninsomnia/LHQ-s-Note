### 配置yum源仓库

```shell
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

### 配置国内docker镜像源

* ```
  vim /etc/docker/daemon.json 
  ```

  在其中添加：

  ```shell
  "registry-mirrors": ["https://m8nvad2m.mirror.aliyuncs.com","http://hub-mirror.c.163.com"]
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

# Docker使用

* 删除所有容器：

  ```shell
  docker rm $(docker ps -aq)
  ```

  
