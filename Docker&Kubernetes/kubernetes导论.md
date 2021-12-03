### 在控制平面节点上调度 Pod（如用于开发的单机 Kubernetes 集群）

* ```shell
  kubectl taint nodes --all node-role.kubernetes.io/master-
  ```

* 这将从任何拥有 `node-role.kubernetes.io/master` taint 标记的节点中移除该标记， 包括控制平面节点，这意味着调度程序将能够在任何地方调度 Pods。

* 

# 高可用

保姆级教程：https://zhuanlan.zhihu.com/p/444114515