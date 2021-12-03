* 官方网站：https://go.dev/blog/using-go-modules
* 中文参考网站（官方网站的翻译版）：https://learnku.com/docs/go-blog/using-go-modules/6485
* Go module基础使用及Go 1.16中的改进：https://zhuanlan.zhihu.com/p/352819668

# Go 1.11 之前的依赖管理系统

* 在不使用额外的工具的情况下，Go的依赖包需要手工下载
* 第三方包没有版本的概念，如果第三方包的作者做了不兼容升级，会让开发者很难受
* 协作开发时，需要统一各个开发成员本地**`$GOPATH/src`**下的依赖包
* 引用的包引用了已经转移的包，而作者没改的话，需要自己修改引用
* 第三方包和自己的包的源码都在src下，很混乱。对于混合技术栈的项目来说，目录的存放会有一些问题

# 基本概念

* 一个Module是**Go packages 的集合**，以 **go.mod 文件**的形式存储在文件树的根目录。
* go.mod 定义了模块的**模块路径**（即**根目录的导入路径**）和 **依赖项需求** （即所需要的其他模块）。每一个依赖项需求都以模块路径和特定语义版本的形式给出。

# 创建模块

* 创建模块之前，需要设置环境变量：

  ```shell
  go env GO111MODULE=auto
  ```

  或者：

  ```shell
  go env GO111MODULE=on
  ```

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

  **Go 1.11~1.15（即1.16版本之前）**：运行**go命令（如go run， go build ， go test）**时， go会**自动解析并下载包**： 

  * import的包在go.mod文件中**有对应的的require描述**，则按对应描述的版本下载

  * import的包在go.mod中**没有require描述**，则按**最新版本**下载该包，同时将该包加入到go.mod中

  **Go 1.16及之后**：运行**go命令（go run, go build,go test）**时，如果import的依赖在go.mod文件中没有，**不会自动下载并修改go.mod和go.sum文件，而会提示错误**，此时就需要手动执行 **`go mod tidy`**或者**`go get`**下载对应的包

* 下载的所有模块都会**被缓存在本地**，位置为**`$GOPATH/pkg/mod`**（**在go mod之前，必须得在`GoPath/src`和`GoRoot/src`搜索相关依赖**）

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

