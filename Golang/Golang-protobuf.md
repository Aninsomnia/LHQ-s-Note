# 下载安装（Windows）

### 编译器

* 下载protoc二进制文件https://github.com/protocolbuffers/protobuf/releases
* 将protoc文件放入path路径

### 库文件

* goprotobuf：

  ```
  go get github.com/golang/protobuf/proto
  ```

* gogoprotobuf：

  ```shell
  go get github.com/gogo/protobuf/proto
  ```

  

### 插件

* protoc-gen-go插件：

  ```shell
  go get github.com/golang/protobuf/protoc-gen-go
  ```

* protoc-gen-gogo插件：

  ```shell
  go get github.com/gogo/protobuf/jsonpb
  go get github.com/gogo/protobuf/protoc-gen-gogo
  go get github.com/gogo/protobuf/gogoproto
  ```



# 使用

* ```
  protoc -I.  -I=D:\Mr.Liao\GoPath\src -I=D:\Mr.Liao\GoPath\pkg\mod\github.com\gogo\protobuf@v1.3.2 --gogo_out=. *.proto
  ```

  https://github.com/go-kratos/kratos/issues/514