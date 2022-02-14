* 官方网站：https://go.dev/blog/using-go-modules
* 中文参考网站（官方网站的翻译版）：https://learnku.com/docs/go-blog/using-go-modules/6485

# 基本概念

* 一个Module是**Go packages 的集合**，以 **go.mod 文件**的形式存储在文件树的根目录。
* go.mod 定义了模块的**模块路径**（即**根目录的导入路径**）和 **依赖项需求** （即所需要的其他模块）。每一个依赖项需求都以模块路径和特定语义版本的形式给出。

# 创建模块

* 使用**`go mod init`**命令将当前目录初始化为一个Module：

  ```shell
  go mod init example.com/hello
  ```

* go.mod文件**仅出现在模块的根目录中**。

* 子目录中的程序包具有**由模块路径以及子目录路径组成的**导入路径。例如：如果我们创建了一个子目录world，该软件包将自动被识别为**`example.com/hello`模块的一部分**，并带有导入路径**`example.com/hello/world`**。

# 依赖管理

### 导入依赖

* go命令通过**使用go.mod中列出的指定模块版本来解析依赖**。

* 当import了一个没有在go.mod中提供的包时，go命令会**自动搜索包含这个包的模块**，并将其**添加到go.mod中**，并且使用这个包的**最新版本**（「最新」指的是包的最新的带标签的稳定版（非预发布版），如果没有稳定版，这使用预发布版，如果都没有，再使用不打标签的最新版本）。

* 例如：

  ```go
  import rsc.io/quote
  ```

  go命令将新添加的导入包rsc.io/quote解析为模块rsc.io/quote v1.5.2。同时，命令还会自动下载rsc.io/quote使用的名为 rsc.io/sampler 和 golang.org/x/text 的两个其余依赖。但是，**只有源码直接依赖的包会被记录到go.mod文件中**。

* 下载的所有模块都会**被缓存在本地**，位置为**`$GOPATH/pkg/mod`**

### 查看依赖

* 使用**`go list -m all`**查看当前模块导入的所有依赖（**包括间接依赖**）：

  ```shell
  go list -m all
  ```

* 在 `go list` 命令的输出中，当前模块，也就是**主模块**，总是出现在第一行，后面按**模块路径的顺序**列出所有依赖

* 除了 `go.mod` 文件， `go` 命令也维护了一个名为 **`go.sum`** 的文件，文件内容为指定版本的**模块内容的 [加密哈希值](https://golang.org/cmd/go/#hdr-Module_downloading_and_verification)** 

### 更新依赖

* 使用**`go get`**命令更新某一依赖：

  ```shell
  go get golang.org/x/text
  ```

* 若更新后依赖不可用，可能是因为**该依赖的最新版本不兼容**，使用**`go list -m -versions`**命令查看某一模块的可用版本：

  ```shell
  go list -m -versions rsc.io/sampler
  ```

* 使用**`go get`**命令更新某一依赖，并指定版本：

  ```shell
  go get rsc.io/sampler@v1.3.1
  ```

  若在go get命令后不显式指定版本号，那么**默认为@latest**

