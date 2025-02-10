# Kubernetes要解决什么问题？

编排？调度？容器云？还是集群管理？

* 实际上，这个问题其实没有固定的答案，因为在不同的发展阶段，Kubernetes 需要着重解决的问题是不同的。
* 但是，我们可以从中找到**不变**的东西，即**对于大多数用户来说，他们希望 Kubernetes 项目带来的体验是确定的**：现在我有了应用的容器镜像，**请帮我在一个给定的集群上把这个应用以容器形式运行起来**。更进一步地说，用户还希望 Kubernetes 能提供**路由网关、水平扩展、监控、备份、灾难恢复**等一系列运维能力。
* 从这一方面看，就可以部分解答上面的问题，Kubernetest要解决什么问题：**如何编排、管理、调度用户提交的作业？**
* 但是，这些功能不就是经典PaaS项目所做的事情吗？所以，如果 Kubernetes 项目**只是停留在拉取用户镜像、运行容器，以及提供常见的运维功能的话**，那么别说跟“原生”的 Docker Swarm 项目竞争了，哪怕跟经典的 PaaS 项目相比也难有什么优势可言。
* 其实，**运行在大规模集群中的各种任务之间，实际上存在着各种各样的关系。这些关系的处理，才是作业编排和管理系统最困难的地方。**这也是Kubernetes与其他PaaS项目最大不同的地方。

* 实际上，过去很多的集群管理项目（比如 Yarn、Mesos，以及 Swarm）所擅长的，都是把一个容器，按照某种规则，放置在某个最佳节点上运行起来。这种功能，我们称为“调度”。而 Kubernetes 项目所擅长的，**是按照用户的意愿和整个系统的规则，完全自动化地处理好容器之间的各种关系。这种功能，就是我们经常听到的一个概念：编排。**所以说，Kubernetes 项目的本质，是为用户提供一个具有普遍意义的容器编排工具。不过，更重要的是，Kubernetes 项目为用户提供的不仅限于一个工具。它真正的价值，乃在于提供了一套基于容器构建分布式系统的基础依赖。

# 定义应用任务之间的各种关系

* Kubernetes 项目最主要的设计思想是：**从更宏观的角度，以统一的方式来定义任务之间的各种关系，并且为将来支持更多种类的关系留有余地。**

### 应用与应用之间的关系

* 我们从容器这个最基础的概念出发：
  * 首先遇到了容器间“紧密协作”关系的难题，它们之间需要非常频繁的交互和访问，或者会直接通过本地文件进行信息交换，于是就扩展到了 Pod；
  * 有了 Pod 之后，我们希望能一次启动多个应用的实例，这样**就需要 Deployment 这个 Pod 的多实例管理器；**
  * 有了这样一组相同的 Pod 后，我们**又需要通过一个固定的 IP 地址和端口以负载均衡的方式（即，代理入口）访问它**（比如Web应用和数据库之间的关系），于是就有了 **Service**。

### 应用运行的形态关系

* 比如 Job，用来描述**一次性运行的 Pod（比如大数据任务）**；
* 比如 DaemonSet，用来描述**每个宿主机上必须且只能运行一个副本的守护进程服务**；
* 比如 CronJob，则用于描述**定时任务**。



# kubelet

### kubelet与static pod

* 在 Kubernetes 中，有一种特殊的容器叫做“Static Pod”。它们的 YAML 文件被放在一个指定的目录里。当kubelet 启动时，它会自动检查这个目录，加载所有的 Static Pod YAML 文件，然后在这台机器上启动它们。
* 从这一点也可以看出，**kubelet 在 Kubernetes 项目中的地位非常高，在设计上它就是一个完全独立的组件，而其他 Master 组件，则更像是辅助性的系统容器。**

# Pod

* Kubernetes中的**最小API对象，即原子调度单位**。
* Pod抽象了进程的间的**“超亲密关系”**：
  * 互相之间会发生直接的文件交换；
  * 使用 localhost 或者 Socket 文件进行本地通信；
  * 会发生非常频繁的远程调用；
  * 需要共享某些 Linux Namespace（比如，一个容器要加入另一个容器的 Network Namespace）等等。

* Pod 里的容器共享同一个 Network Namespace、同一组数据卷，从而达到高效率交换信息的目的。

### Pause容器

* Pod中的各个业务容器需要共同的 namespace 和资源环境，但是在生命周期管理过程中，**又不想让它们存在逻辑拓扑关系（即先创建一个业务容器，然后再将另一个容器加入到该容器中）**，故而引入一个中间层Pause容器。
* Pause容器永远是Pod中首先被创建的容器，它的作用主要是为 Pod 中的其他容器**提供一个共享的 namespace 和资源环境**，其他容器**可以通过加入该环境来进行本地通信和资源共享。**

### 容器编排模式

* 将业务中的依赖先后顺序进行梳理，将业务编排转变为容器编排，最终输出为yaml文件。
* 尽量尝试使用此方法**来描述和解决一些用单个容器难以解决的问题。**

### sidecar模式

* 可以在一个 Pod 中，启动一个**辅助容器**，来完成一些独立于主进程（主容器）之外的工作：
  * **为主容器提前准备好某些文件、目录**：当文件、目录需要变更时，只需要更新该辅助容器的版本，然后重启Pod即可；
  * **为主容器收集日志**：通过共同挂载同一volume的方式，收集主容器日志，并将其输出至其他组件保存起来。

### Pod属性

将Pod想象为传统模式中的“服务器”，将容器想象成传统模式中的“进程”，那么**关于“机器”的网络、存储、安全和调度等属性，肯定都是属于Pod层面的属性。**

###### NodeName

* 该字段**一般由Scheduler来设置**，用于标记已调度的结果，但也可以手动设置，用于跳过Scheduler调度。

###### HostAliases

* 用于设置`/etc/hosts`中的内容：

  ```yaml
  apiVersion: v1
  kind: Pod
  ...
  spec: 
  	hostAliases: 
  	- ip: "10.1.2.3" 
  	  hostnames: 
  	  - "foo.remote" 
  	  - "bar.remote"
  ```

  这样，容器中/etc/hosts文件内容就是这样：

  ```shell
  cat /etc/hosts
  # Kubernetes-managed hosts file.
  127.0.0.1 localhost
  ...
  10.244.135.10 hostaliases-pod
  10.1.2.3 foo.remote
  10.1.2.3 bar.remote
  ```

* 注意：**这是在kubernetes中设置 hosts 文件里的内容的最好办法**，因为假如在容器启动后修改/etc/hosts内容，那么当Pod故障重启后，`/etc/hosts`内容将会丢失，**因为该文件处于容器的init层**。

###### status.phase

* 对于 Pod 状态是 Ready，实际上不能提供服务的情况能想到几个例子： 
  * 程序本身有 bug，本来应该返回 200，但因为代码问题，返回的是500；
  * 程序因为内存问题，已经僵死，但进程还在，但无响应；
  * Dockerfile 写的不规范，应用程序不是主进程，那么主进程出了什么问题都无法发现；
  * 程序出现死循环。

# Projected Volume

它们存在的意义**不是为了存放容器里的数据，也不是用来进行容器和宿主机之间的数据交换**，而是**为容器提供预先定义好的数据**。所以，从容器的角度来看，这些 Volume 里的信息就是仿佛是被“投射”（Project）进入容器当中的。

* secret、configmap和downwardAPI三种获取信息的方式，和通过环境变量的方式，效果是一样的；不过前者可以自动更新，后者并不能自动更新。

### secret

### configMap

### downwardAPI

作用是让 Pod 里的容器能够直接获取到这个 Pod API 对象本身的信息。

* 如以下例子，该Downward API Volume，声明了要暴露 Pod 的 metadata.labels 信息给容器：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-downwardapi-volume
  labels:
    zone: us-est-coast
    cluster: test-cluster1
    rack: rack-22
spec:
  containers:
    - name: client-container
      image: k8s.gcr.io/busybox
      command: ["sh", "-c"]
      args:
      - while true; do
          if [[ -e /etc/podinfo/labels ]]; then
            echo -en '\n\n'; cat /etc/podinfo/labels; fi;
          sleep 5;
        done;
      volumeMounts:
        - name: podinfo
          mountPath: /etc/podinfo
          readOnly: false
  volumes:
    - name: podinfo
      projected:
        sources:
        - downwardAPI:
            items:
              - path: "labels"
                fieldRef:
                  fieldPath: metadata.labels
```

* 需要注意的是，Downward API 能够获取到的信息，**一定是 Pod 里的容器进程启动之前就能够确定下来的信息**；而如果想要获取 Pod 容器运行后才会出现的信息（例如容器进程的 PID），那就肯定不能使用 Downward API 了，而应该考虑在 Pod 里定义一个 **sidecar 容器**。

### serviceAccountToken

### clusterTrustBundle

# ServiceAccount

* 在k8s中，所有访问Apiserver的操作，都必须进行身份认证，而**ServiceAccount就是k8s中的一种身份标识，用于识别不同访问类型，并赋予不同的访问权限。**
* Service Account 的授权信息和文件（**集群的CA证书、ServiceAccount所在名称空间和访问API Server的token**，一共三个信息），实际上保存在它所绑定的一个特殊的 Secret 对象里的，即**ServiceAccountToken**。任何运行在 Kubernetes 集群上的应用，都必须使用这个 ServiceAccountToken 里保存的授权信息，才可以合法地访问 API Server。

### 1.24以后SA和SAToken行为变更

* 1.21之前，创建SA时会**自动为其创建一个对应的ServiceAccountToken，创建Pod的时候这个Secret也被自动挂载到Pod中**，用于Pod中的应用访问API Server。

* 1.21~1.23这几个版本里，创建sa时也会自动创建secret（里面的Token依旧永不过期），但是在pod里并不会使用该secret里的token，而是**由kubelet到tokenRequest api去申请一个token**。

* 1.24及以后，不再自动为 SA 生成 token 并保存到 secret 中，Pod 中使用 token 时也不会再挂载这个 secret：

  * 查看Pod信息：

  ```yaml
    volumes:
    - name: kube-api-access-652fr
      projected:
        defaultMode: 420
        sources:
        - serviceAccountToken:
            expirationSeconds: 3607
            path: token
        - configMap:
            items:
            - key: ca.crt
              path: ca.crt
            name: kube-root-ca.crt
        - downwardAPI:
            items:
            - fieldRef:
                apiVersion: v1
                fieldPath: metadata.namespace
              path: namespace
  ```

  * 可以看到Pod定义了一个卷kube-api-access-xxx，并且也挂载到容器内`/var/run/secrets/kubernetes.io/serviceaccount` 目录下；
  * 但卷的类型变为了projected，且数据来源并不是Secret：
    * token是**由kubelet到tokenRequest api去申请的token，且token的有效期为1小时；**
    * ca.crt来自configmap，这个kube-root-ca.crt在每个名称空间都存在，创建新namespace时k8s会自动在namespace下添加；
    * namespace是通过downwardAPI插件获取Pod自身的字段值。

* 1.24要想获得一个永久Token的最简单方法就是手动创建一个Secret。

* 参考链接：

  * https://blog.csdn.net/weixin_43266367/article/details/127304422
  * https://www.myway5.com/index.php/2023/01/18/k8s-1-24-serviceaccount-token-changes/

### 1.24 SAToken行为变更导致的问题

* CNI 组件Calico Pod 会将自己的 ServiceAccountToken 转换成 kubeconfig 并存储到主机的目录下。
* 当 kubelet 调用 cni 插件时，cni 插件会使用这个 kubeconfig 去获取集群 Pod 的一些信息。
* 当 CNI Pod 重启后，使用生成的 kubeconfig 就会返回 Unauthorized 的错误，即这个 token 已经过不了 APIServer 的认证了。

# Pod状态检查

### 健康探针检查（livenessProbe）

* 检查容器是否**存活**，以此来决定是否要重启容器
* 为 Pod 里的容器定义一个健康检查探针（livenessProbe），kubelet 就会根据这个探针的**返回值**决定这个容器的状态，而不是直接以容器是否运行（来自 Docker 返回的信息）来作为依据。
* 这样，**决定容器应用是否正常运行的权力就交到了开发者手上**，镜像制作者可以根据业务实际情况和需求，来决定如何判断应用功能是否正常。
* **健康检查探针可以是执行命令行、发起HTTP请求或者是发起TCP请求。**
* 当kubelet发现Probe探针失败时，就会根据Pod恢复机制策略进行自动恢复。

### 就绪探针检查（readinessProbe）

* 检查容器是否**就绪**，以此来决定是否可以接收流量
* readinessProbe 检查结果的成功与否，**决定的是这个 Pod 是不是能被通过 Service 的方式访问到，而并不影响 Pod 的生命周期**。



# 容器恢复机制

### 恢复机制（restartPolicy）

* Always：在任何情况下，只要容器不在运行状态，就自动重启容器，哪怕容器正常执行完毕，Pod状态变为了Succeeded，此时也会重启容器；
* **OnFailure**: 只在容器异常时（例如报错返回非0值）才自动重启容器；
* Never: 从来不重启容器。
* 特别注意：
  * **Pod 的恢复过程永远都是发生在当前节点上**，并不会将该Pod重新调度到其余节点上。
  * 事实上，一旦 Pod 与一个节点绑定（即**`pod.spec.node` 字段被固定**），除非这种绑定发生了变化，否则它永远都不会离开这个节点，哪怕该节点宕机，该Pod也不会被迁移到其余节点上去。
  * 如果想让 Pod 出现在其他的可用节点上，就必须使用**控制器**来管理 Pod（例如Deployment），哪怕只有一个 Pod 副本。

### 恢复机制两条基本原则

* **只要 Pod 的 restartPolicy 指定的策略允许重启异常的容器（Always或者OnFailure），那么这个 Pod 就一定会保持 Running 状态，并进行容器重启。**
* **只有 Pod 所有的容器都进入异常状态后，并且 restartPolicy 为 Never，Pod 才会进入 Failed 状态，否则Pod 都是 Running 状态。**

# 控制器模式

### 控制循环（control loop）

* 所有控制器都遵循 Kubernetes 项目中的一个通用编排模式，即控制循环：

  ```golang
  for {
    实际状态 := 获取集群中对象X的实际状态（Actual State）
    期望状态 := 获取集群中对象X的期望状态（Desired State）
    if 实际状态 == 期望状态{
      什么都不做
    } else {
      执行编排动作，将实际状态调整为期望状态
    }
  }
  ```

* **实际状态往往来自于 Kubernetes 集群本身**，比如：

  * kubelet 通过心跳汇报的**容器状态和节点状态**；
  * 监控系统中保存的应用监控数据；
  * **控制器主动收集的它自己感兴趣的信息**。

* **期望状态往往来自于用户的YAML文件**。

* 控制器将实际状态向期望状态更改的动作，通常被叫作**调谐（Reconcile）**或者**同步循环（Sync Loop）**。

* 调谐的最终结果，往往都是对被控制对象的某种**写操作**（比如，增加 Pod，删除已有的 Pod，**或者更新 Pod 的某个字段**）。这也是 Kubernetes 项目**“面向 API 对象编程”**的一个直观体现。

* **正是在这个统一的编排框架下，不同的控制器可以在具体执行过程中，设计不同的业务逻辑，从而达到不同的编排效果**。

# ReplicaSet

* 作业副本的水平扩展和收缩是一个平台级项目就必须具备的编排能力，而Kubernetes中，依赖的就是ReplicaSet。
* **Deployment、DaemonSet等控制器实际操纵的，正是这样的 ReplicaSet 对象，而不是 Pod 对象。**

# Deployment

Deployment 的控制器，**实际上控制的是 ReplicaSet 的数目，以及每个 ReplicaSet 的属性**。

### 单Pod Deployment

一个单独的 Pod 与一个 replicas=1 的 Deployment之间的区别：

* 两者主要的区别在于**Pod的故障处理手段**：

  * 当Pod故障时，单独Pod只能够在节点健康的情况下，由**本节点kubelet去保证其健康状态**；

  * 而replicas=1的Deployment则可以由自己去保证其Pod健康状态，并且**可以将其在其他节点上拉起，同时也可以自定义重启恢复策略**。

### 水平扩展与收缩

* Pod副本的水平扩展与收缩比较简单，Deployment只需要修改其控制的ReplicaSet中Pod的副本数量即可。

### 滚动更新

* Deployment的滚动更新操作，是**通过不停地修改新旧ReplicaSet期望状态实现的**，以一个三Pod副本的Deployment进行版本更新为例：
  * Deployment Controller会首先创建一个新的ReplicaSet，**但是其Pod的副本数为0**；
  * 然后，**Deployment Controller将该新ReplicaSet的Pod副本数改为1**；
  * 当第一个新版本Pod处于Running状态且健康检查正常后，Deployment Controller将旧ReplicaSet的Pod副本数由3改为2；
  * 当老版本Pod被删除一个后，又再将新ReplicaSet的Pod副本数增加1；
  * 如此循环往复，直到3个新版本Pod正常运行，且3个老版本Pod全部被删除为止。
* 为了进一步保证服务的连续性，Deployment Controller 还会确保，**在任何时间窗口内，只有指定比例的 Pod 处于离线状态，且只有指定比例的新 Pod 被创建出来**。这两个比例的值都是可以配置的，**默认都是 DESIRED 值的 25%**。

### 滚动更新策略

* RollingUpdateStrategy：

  * maxSurge：除了 DESIRED 数量之外，在一次“滚动”中，Deployment 控制器还可以创建多少个新 Pod；

  * maxUnavailable：在一次“滚动”中，Deployment 控制器可以删除多少个旧 Pod。

  * 同时，这两个配置还可以用前面我们介绍的百分比形式来表示，比如：maxUnavailable=50%，指的是我们最多可以一次删除“50%*DESIRED 数量”个 Pod。


# StatefulSet

* **实例之间有不对等关系**，以及**实例对外部数据有依赖关系**的应用，就被称为“有状态应用”（Stateful Application）。如：
  * 实例之间存在主从关系、主备关系；
  * 数据存储类应用。

* StatefulSet 给它所管理的所有 Pod 的名字，进行了编号，**从 0 开始累加，与 StatefulSet 的每个 Pod 实例一一对应，绝不重复**。
* **Pod 的创建**严格按照编号顺序进行（比如：在 web-0 进入到 Running 状态、并且细分状态（Conditions）成为 Ready 之前，web-1 会一直处于 Pending 状态）。
* Pod内部的hostname也是按照该编号命名。
* 这样，Kubernetes 就成功地将 Pod 的拓扑状态，按照 Pod 的**“名字 + 编号”**的方式固定了下来。
* 此外，Kubernetes 还通过**Headless Service**的方式，**为每一个 Pod 提供了一个固定并且唯一的访问入口**，即：这个 Pod 对应的 **DNS 记录**。

# DaemonSet

* 各种**网络插件的 Agent 组件**，必须运行在每一个节点上，用来处理这个节点上的容器网络；
* 各种**存储插件的 Agent 组件**，必须运行在每一个节点上，用来在这个节点上挂载远程存储目录，操作容器的 Volume 目录；
* 各种**监控组件和日志组件**，必须运行在每一个节点上，负责这个节点上的监控信息和日志搜集。

### Affinity与Toleration

* 在创建每个 Pod 的时候，DaemonSet Controller会自动为该 Pod 加上一个 nodeAffinity，从而保证这个 Pod 只会在指定节点上启动。
* 也会自动给这个 Pod 加上一个 Toleration，从而忽略节点的 unschedulable“污点”。

# 容器运行时

### CRI（Container Runtime Interface）

* 在 Kubernetes 项目中，**kubelet** 主要负责同容器运行时（比如 Docker）打交道。这个交互所依赖的，是一个称作 **CRI（Container Runtime Interface）**的远程调用接口，**这个接口定义了容器运行时的各项核心操作，比如：启动一个容器需要的所有参数。**
* Kubernetes 项目并不关心部署的是什么容器运行时、使用的什么技术实现，只要某个容器运行时能够运行标准的容器镜像，它就可以通过实现 CRI 接入到 Kubernetes 项目当中。
* 而具体的容器运行时，比如 Docker，则一般通过 OCI 这个容器运行时规范同底层的 Linux 操作系统进行交互，即：**把 CRI 请求翻译成对 Linux 操作系统的调用（操作 Linux Namespace 和 Cgroups 等）**。

# Service

* Service就是将一组Pod暴露给外部访问的机制。

### 访问Service的方式

* VIP
* DNS

# 声明式API

### 命令响应式请求与声明式请求的差别

* **命令响应式请求（如kubectl replace）**是用新的API对象替换原有对象，整个过程就是一个**HTTP事务**，会触发数据库写锁，所以同一时间只能进行一次请求处理。
* **声明式请求（如kubectl apply）**是对原有 API 对象的 **PATCH 操作**，只要不同时操作同一个元素，就不会引起冲突。

### 具体意义

* 首先，“声明式”指的就是**只需要提交一个定义好的 API 对象来“声明”所期望的状态是什么样子。**
* 其次，“声明式 API”**允许有多个 API 写端**，以 **PATCH** 的方式对 API 对象进行修改，而无需关心原始对象的内容。
* 最后，也是最重要的，有了上述两个能力，Kubernetes 项目才可以在完全无需外界干预的情况下，**完成对“实际状态”和“期望状态”的调谐（Reconcile）过程。**

# API对象

### API对象组成

* 一个 API 对象在 Etcd 里的完整资源路径，是由三个部分组成：
  * **Group（API 组）**
  * **Version（API 版本）**
  * **Resource（API 资源类型）**

### API对象解析过程

以CronJob为例：

```yaml
apiVersion: batch/v2alpha1
kind: CronJob
...
```

* **首先，Kubernetes 会匹配 API 对象的组**：
  * 对于 Kubernetes 里的**核心 API 对象**，比如：Pod、Node 等，是不需要 Group 的（即：它们的 Group 是“”）。对于这些 API 对象来说，Kubernetes 会直接在 /api 这个层级进行下一步的匹配过程。
  * 对于 CronJob 等**非核心 API 对象**，Kubernetes 就必须在 **/apis** 这个层级里查找它对应的 Group，进而根据“batch”这个 Group 的名字，找到 /apis/batch。
* **然后，Kubernetes 会进一步匹配到 API 对象的版本号。**
* **最后，Kubernetes 会匹配 API 对象的资源类型。**

# Admission Control

* 在 Kubernetes 项目中，当**任何一个 API 对象**被提交给 APIServer 之后，总有一些“初始化”性质的工作需要在它们被 Kubernetes 项目正式处理之前进行（比如自动为 Pod 加上某些Label）。
* 这个“初始化”操作的实现，使用的是一个叫作 **Admission** 的功能，可以选择性地被编译进 APIServer 中，在 API 对象创建之后会被立刻调用到。
* 但想要添加一些自己的规则到 Admission Controller，就会比较困难，因为，这**要求重新编译并重启 APIServer**。

# Initializer（Dynamic Admission Control）

（以Istio为例）

* 首先，Istio 会将这个 Envoy 容器本身的定义，**以 ConfigMap 的方式保存在 Kubernetes 当中。**
* 在 Initializer 更新用户的 Pod 对象的时候，必须使用 **PATCH API** 来进行两者的merge。**这种 PATCH API，正是声明式 API 最主要的能力。**
* 将一个编写好的 Initializer作为一个 Pod 部署起来，该 Pod 其实就是一种**自定义控制器（controller）**，不断获取用户Pod信息（实际状态），再向里面merge Envoy容器配置（期望状态）。

















# 在控制平面节点上调度 Pod（如用于开发的单机 Kubernetes 集群）

* ```shell
  kubectl taint nodes --all node-role.kubernetes.io/master-
  ```

* 这将从任何拥有 `node-role.kubernetes.io/master` taint 标记的节点中移除该标记， 包括控制平面节点，这意味着调度程序将能够在任何地方调度 Pods。

* 

# 高可用

保姆级教程：https://zhuanlan.zhihu.com/p/444114515



# 命令使用

### kubectl apply

* 用于创建或更新资源。
* 这是 Kubernetes **“声明式 API”**所推荐的使用方法：
  * 用户不必关心操作是创建，还是更新，执行的命令始终是 kubectl apply，Kubernetes 则会**根据 YAML 文件的内容变化，自动进行具体的处理。**
  * 这有助于帮助开发和运维人员，**围绕着可以版本化管理的 YAML 文件，而不是“行踪不定”的命令行进行协作**，从而大大降低开发人员和运维人员之间的沟通成本。

### kubectl replace

* 用于替换现有资源。
* 首先删除现有资源，然后直接替换资源的定义，而不会去检查现有状态。
* **如果现有资源不存在，命令会失败，并且如果新资源定义错误，原有资源也无法被恢复。**