### etcd安装clientv3报错

* 安装过程中可能会出现以下类似问题：

  ```shell
  /root/go/pkg/mod/github.com/coreos/etcd@v3.3.25+incompatible/clientv3/balancer/picker/roundrobin_balanced.go:55:54: undefined: balancer.PickOptions
  # github.com/coreos/etcd/clientv3/balancer/resolver/endpoint
  /root/go/pkg/mod/github.com/coreos/etcd@v3.3.25+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:114:78: undefined: resolver.BuildOption
  /root/go/pkg/mod/github.com/coreos/etcd@v3.3.25+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:182:31: undefined: resolver.ResolveNowOption
  ```

* 如上问题，是因为包冲突了 ，只需要将如下替换包的命令放到 我们 `go.mod` 下面即可：

  ```
  replace google.golang.org/grpc => google.golang.org/grpc v1.26.0
  ```

  