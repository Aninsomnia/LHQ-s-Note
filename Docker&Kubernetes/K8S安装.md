# 通过Kubeadm创建K8S集群（Bootstrapping clusters with kubeadm）

* Kubernetes官网地址：https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/
* 参考网站：
  * [使用kubeadm在Centos8上部署kubernetes1.18](https://www.kubernetes.org.cn/7189.html)
  * 知乎：https://zhuanlan.zhihu.com/p/55740564?from_voters_page=true
  * 语雀：https://www.yuque.com/leifengyang/oncloud/ghnb83
  * https://dayongchan.com/2019/11/17/install-k8s/

### 安装Kubeadm

##### 配置yum的kubenetes源仓库

* 官方：

```shell
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
```

* 国内

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
 http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF
```

##### 关闭SELinux

```shell
#Set SELinux in permissive mode (effectively disabling it)

sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

##### 安装kubeadm

```shell
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

sudo systemctl enable kubelet
sudo systemctl restart kubelet

```

* 此时kubelet会因为等待kubeadm指示而陷入不断重启的阻塞循环。

##### kubeadm init

* 查看init需要拉取的镜像

  ```shell
  kubeadm config images list 
  #specify version
  kubeadm config images list --kubernetes-version=1.21.0
  
  kubeadm config print init-defaults 
  kubeadm init --config kubeadm-config.yaml
  ```

* 在kubeadm init时不能直接访问默认仓库k8s.grc.io，从而导致init失败：

  * ①：在init时指定镜像仓库

  ```shell
  kubeadm init --image-repository registry.aliyuncs.com/google_containers --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=47.109.100.96
  ```

  ```shell
  kubeadm init --image-repository registry.cn-hangzhou.aliyuncs.com/google_containers --pod-network-cidr=10.123.0.0/16 --apiserver-advertise-address=47.109.100.96
  ```

  

  * ②：手动下载k8s.grc.io各镜像，然后重新tag

    ```
    镜像仓库为registry.cn-hangzhou.aliyuncs.com/google_containers
    ```

    

* 如果在init时，containerd为running：

  ```
  mkdir -p /etc/containerd && containerd config default > /etc/containerd/config.toml
  systemctl restart containerd
  ```

* 查看kubelet日志：

  ```
  tail /var/log/messages
  journalctl -xeu kubelet
  ```

* init成功后，设置配置文件：

  * 对于非`root`用户：

  ```bash
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  ```

  * 对于`root` 用户：

  ```bash
  export KUBECONFIG=/etc/kubernetes/admin.conf
  ```

* `kubeadm init`成功后，会给出其他节点想加入集群时需执行的`kubeadm join`指令：

  https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#join-nodes
  
* 此时集群节点状态为**NotReady**

### 安装CNI插件

* Calico：

  ```shell
  kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
  ```

### 清理集群

* 应首先清空**集群中的结点**：https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

  ```shell
  kubectl drain <node name> --delete-emptydir-data --force --ignore-daemonsets
  kubectl drain <node name> --delete-emptydir-data --force --ignore-daemonsets
  ```

* 再**对控制平面进行清理**：https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-reset/

  * 对于CentOS：

  ```shell
  kubeadm reset -f
  
  sudo yum remove -y kubeadm kubectl kubelet kubernetes-cni kube*
  sudo yum autoremove
  
  sudo rm -rf ~/.kube
  ```

  * 对于Debian：

  ```shell
  kubeadm reset 
  
  sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube* 
  sudo apt-get autoremove
   
  sudo rm -rf ~/.kube
  ```

* 或者（来自CSDN）：

  ```shell
  kubeadm reset -f
  modprobe -r ipip
  lsmod
  rm -rf ~/.kube/
  rm -rf /etc/kubernetes/
  rm -rf /etc/systemd/system/kubelet.service.d
  rm -rf /etc/systemd/system/kubelet.service
  rm -rf /usr/bin/kube*
  rm -rf /etc/cni
  rm -rf /opt/cni
  rm -rf /var/lib/etcd
  rm -rf /var/etcd
  yum clean all
  yum remove kube*
  ```

  