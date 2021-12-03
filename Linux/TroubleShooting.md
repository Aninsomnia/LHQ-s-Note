# sudo更改文件，权限报错xxx is not in the sudoers file. This incident will be reported.

### 解决方法：在/etc/sudoers文件里给该用户添加权限

* 切换到root用户下：

  ```shell
  su root
  ```

* 添加sudoers文件的**写权限**（因为/etc/sudoers文件默认是只读的，对root来说也是）：

  ```shell
  chmod u+w /etc/sudoers
  ```

* 编辑sudoers文件：

  ```shell
  vim /etc/sudoers
  ```

* 找到 `root ALL=(ALL) ALL`，在下面添加`xxx ALL=(ALL) ALL` (这里的xxx是用户名)

  可以向sudoers添加下面四行中任意一条

  * `username ALL=(ALL) ALL`，允许用户`username`执行`sudo`命令(需要输入密码).
  * `%usergroup ALL=(ALL) ALL`，允许用户组`usergroup `里面的用户执行`sudo`命令(需要输入密码).
  * `username ALL=(ALL) NOPASSWD: ALL`，允许用户`username`执行`sudo`命令,并且在执行的时候不输入密码.
  * `%usergroup ALL=(ALL) NOPASSWD: ALL`，允许用户组`usergroup`里面的用户执行`sudo`命令,并且在执行的时候不输入密码.

* 撤销sudoers文件写权限：

  ```shell
  chmod u-w /etc/sudoers
  ```
  
  