####  常见问题

* 常用的命令
  * **chmod：修改目录或文件的权限命令**
  * mkdir：创建目录
  * touch：创建新的文件
  * rm -rf：删除文件
  * cp：复制文件
  * **ls：查看文件夹内容，不同文件类型加不同颜色**
  * **dir：查看文件夹内容**
  * **tar：给文件打包，但不压缩**
  * Gzip：对文件压缩或解压缩，后缀为.gz
  * **su：切换用户**
  * **sudo：以管理员身份执行**
  * **ifconfig：查看IP地址**
  * **tracert：traceroute命令**
  * chroot：切换根目录
  * **ps -rf  / ps -aux：查看进程**
  * **sed / cut / awk : 文本编辑命令**
  * journal -xe：查看日志及其相关信息

# 常用指令

### 主机名

* 查看主机名：

  ```shell
  hostname
  hostnamectl
  ```

* 更改主机名：

  * 修改`/etc/hostname`文件：

    ```shell
    echo "MyLinux" > /etc/hostname 
    ```

    这种方法需要**服务器重启**才能生效。

  * 使用`hostnamectl`命令：

    ```shell
    hostnamectl set-hostname MyLinux
    ```

## MV

* 将`/usr/udt/`下的所有文件移到当前目录下：

  ```shell
  mv /usr/udt/* .
  ```

  

# 环境变量

### 查看环境变量

* 查看所有环境变量：

  ```shell
  export
  ```

* 查看特定环境变量：

  ```shell
  echo $PATH
  ```

### 设置环境变量

##### 使用`export`直接设置：

* ```shell
  export PATH=/usr/local/go/bin:$PATH
  
  # 或者把PATH放在前面
  export PATH=$PATH:/usr/local/go/bin
  ```

* 生效时间：**立即生效**

* 生效期限：**当前shell终端有效，终端关闭后无效**

* 生效范围：**仅对当前用户有效**

##### 修改`/etc/profile`文件：

* 若权限不够，先**修改权限**：

  ```shell
  # 如果/etc/profile文件不可编辑，需要修改为可编辑
  chmod -v u+w /etc/profile
  ```

* 进入`/etc/profile`文件：

  ```shell
  vim /etc/profile
  # 在最后一行加上
  export PATH=$PATH:/usr/local/go/bin
  ```

* 修改后还需使其生效：

  ```shell
  source /etc/profile
  ```

* 生效时间：**下次终端生效，若想立即生效，需运行`source /etc/profile`**

* 生效期限：**永久有效**

* 生效范围：**对所有用户有效**