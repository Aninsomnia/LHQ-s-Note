# flag库

用于命令行解析

### flag.Type()

* 使用flag.Type()初始化参数key和value：

  ```go
  func main(){
  	// 第一个是参数key，第二个是默认值，第三个是 .exe -h 提示
  	var usr = flag.String("usr", "root", "用户名")
      var port = flag.Int("port"， 3306， "端口") 
      var ip = flag.String("ip"， "localhost"， "ip地址")
      
      //必须使用flag.Parse()解析一下命令行参数 
      flag.Parse() 
      //flag.Type返回的是一个指针
      fmt.Println(*user，*port，*ip) 
  }
  ```

### flag.TypeVar()

* ```go
  func main() { 
      //声明变量用于接收命令行参数 
      var user string 
      var port int 
      var ip string 
   
      //从命令行扫描参数赋值到变量 
      flag.StringVar(&user， "user"， "root"， "用户名") 
      flag.IntVar(&port， "port"， 3306， "端口") 
      flag.StringVar(&ip， "ip"， "localhost"， "mysql ip") 
   
      //必须使用flag.Parse()解析一下命令行参数 
      flag.Parse() 
      //flag.Type返回的是一个指针，必须通过 *变量取值 
      fmt.Println(user， port， ip) 
  } 
  ```

  