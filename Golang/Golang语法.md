# 基础语法

* go语言语义中，`if`、`switch`、`for`等循环分支关键词后**不跟括号**。
* 函数定义、`if`、`switch`、`for`后跟的函数体、循环体等内容的第一个花括号**`{`**必须要**和关键词同一行**。

### switch case

* C、Java中的`switch case`，会**尝试匹配每一个`case`条件**，匹配成功执行`case`的body内容后，**会继续向下匹配**，我们需要**使用`break`语句跳出`case`**，使匹配成功后不继续向下判断。

* 而Go语言刚好相反，也**会尝试匹配每一个`case`条件**，但在匹配成功执行`case`的body内容后，**不会继续向下匹配**，在某些情形下我们需要**使用`fallthough`语句直接执行下一个`case`的body内容（注意不是继续向下判断，而是直接执行下一个case体内容）**。

* ```go
  	str := "hello World"
  	switch {
  	case strings.Contains(str, "hello"):
  		fmt.Println("no")
  		fallthrough
  	case strings.Contains(str, "111"):
  		{
  			fmt.Println("yes")
  		}
  		fallthrough
  	default:
  		fmt.Println("neither")
  	}
  ```

### for循环

* ```go
  	for i := 0; i < 10; i++ {
  	}
  	
  	for {
  	}
  
  	for strings.Contains(str, "hello") {
  	}
  ```


# 常量声明 const

* Go中常量的声明可以不指定类型，当**不指定类型时，默认为`untyped`类型**：

  ```go
  	const i int = 12 // i为int类型
  	const j = 12 // j为untyped类型
  ```

* 注意：不指定类型的`untyped`类型常量是**不能作为函数实际传入的参数的。**

* Go中所有字面值都可以直接使用，这在编译器中是以`big`包为支持的：

  ```go
  	fmt.Printf("%s%d", "My name is Liao ,age is ", 20)
  	fmt.Println("age is ", 2000000000000000000)
  ```

# 变量声明 var

* **全局变量可以声明不使用，但局部变量声明后必须使用。**

### 类型种类

* int int8 int16 int32 int64

* uint8 ...

* float32

* `float64`

  * 在Go中，只要用**指数形式**表示的数字，默认都是**`float64`**类型：

  ```go
  	i := 2e2
  	fmt.Println(i)
  ```

  * 某些时候，**unit64**都无法存储的很大的数，可以使用`float64`类型进行存储。

* ......

### 类型别名

* 类型别名就是同一种类型的多个名字，如：

  * **`rune`**是**`int32`**的类型别名。
  * **`byte`**是**`uint8`**的类型别名。

* 可以使用`type`关键字声明某个类型的类型别名：

  ```go
  	type myint int64
  ```

* **注意**：**一个类型和它的类型别名底层使用的是同一种类型**，所以**它们各自和`untyped`类型的常量或者字面量之间**的赋值、运算操作可以正常进行。但是，它们之间依旧是两种不同类型，在不经过强制转换的情况下，**类型和类型别名之间、类型别名和非`untyped`常量之间**的一些赋值、运算操作将不允许。

  ```go
  	type myint int
  	var i myint
  	i = 20 // 正常运行
  	i += 20 // 正常运行
  
  	const k = 20
  	i = k // 正常运行
  	i += k // 正常运行
  
  	const m int = 20
  	i = m // 报错
  	i += m // 报错
  
  	var j int = 20
  	i = j // 报错
  	i += j // 报错
  
  	i = myint(j) // 正常运行
  	i += myint(j) // 正常运行
  ```

### 类型推导

* 声明时可以通过值来推导类型：

* ```go
  var str = "Hello World!"
  ```

### 简短类型声明

* ```go
  str := "HelloWorld!!"
  ```

* **只能在函数中使用**，不能在全局中使用。

* 在一些**不能使用`var`关键字的地方**，只能使用简短类型声明：

  * `for`循环

    ```go
    	for i := 0; i < 10; i++ {
    		fmt.Println(i)
    	}
    ```

  * `if`：

    ```go
    	if num := rand.Intn(10); num == 0 {
    	}
    ```
    
  * `switch`：
  
    ```go
    	switch num := rand.Intn(10); num {
    	case 0:
    	}
    ```

### 平行赋值

* ```go
  i, j := 10, 20
  ```

* 使用平行赋值可以**很方便地交换两个变量的值**，而其余语言（比如C）则需要进行多步操作

  ```go
  i, j = j, i
  ```

### 默认零值

* GO语言中的**所有变量都拥有一个默认值**，如果声明后并不对其做任何赋值，则变量的值就为该默认值。

### rune类型

* `rune`是`int32`的**类型别名**，专门用于表示各种字符：

  ```go
  	var j rune = 960
  	fmt.Printf("%c", j) // 打印字符π
  ```

### float类型

* 大多数语言都存在着浮点类型运算时的误差

* java：

  ```java
      double f1 = 1.0/3;
      System.out.println(f1 + f1 + f1);
  
      double f2 = 0.1;
      f2 += 0.2;
      System.out.println(f2);
  ```

  运算结果：

  ```java
  1.0
  0.30000000000000004
  ```

* Go:

  ```go
  	third := 1.0 / 3
  	fmt.Println(third + third + third)
  
  	tmp := 0.1
  	tmp += 0.2
  	fmt.Println(tmp)
  ```

  运算结果：

  ```go
  1
  0.30000000000000004
  ```

* 所以，**浮点类型不适用于金融类型等高进度要求的计算。**

* 为了尽量最小化舍入错误，**应先做乘法再做除法**。

### string类型

* 在普通字符串中，**转义字符会被转义为特定的格式，而不在字符串中显示出来**，若想在字符串中显示，需要使用**原始字符串字面值**：

  ```go
  	str1 := "My name is Liao \n my age is 20"
  	str2 := `My name is Liao \n my age is 20`
  ```

  若想在原始字符串字面值中换行则只需要在定义时多行定义即可：

  ```go
  str1 := "My name is Liao \nmy age is 20"
  fmt.Println(str1)
  fmt.Println(`My name is Liao 
  my age is 20`)
  ```

* 值得注意的是，可以为一个变量赋上不同的字符串值，**但是字符串本身是不可变的**：

  ```go
  	str := "Hello World"
  	fmt.Printf("%c", str[5])
  	str[5] = 'q'
  ```

  上述最后一行代码会报错，因为**"Hello World"这个字符串不允许被修改**。

# 关键字

### range

* 使用`range`关键字可以遍历某个集合，**返回其中每个元素的索引和值。**

  ```go
  	arr := [...]int{1, 2, 3}
  	fmt.Println(len(arr))
  
  	for index, num := range arr {
  		fmt.Println(index, num)
  	}
  ```

* 注意：使用**range**遍历**字符串**时，返回的索引是**所占内存空间的地址索引**，而**不是字符串中的字符索引（因为某些字符在utf-8中的长度大于一字节，这会导致字符串中某些字符的字符索引要小于所占内存空间的地址索引）。**

### defer

* 使用`defer`关键字，可以保证所有**defered的函数或方法**，在函数返回前被执行。
* 可以消除**必须时刻惦记进行资源释放的负担。**

# 内置包

### len()

* 返回字符串所占用的**字节数**（**注意是字节数，不是字符数**，因为Go语言**使用UTF-8编写，而UTF-8是可变长度编码，不同字符所占用的空间不同，所以字符串的长度和所占字节数不一定一致**）。
* 若要对多语言文本进行操作，可以将其转换为`rune`类型，然后进行统一操作。可以使用**`unicode/utf8`**这个包对字符串进行操作。

### error接口

* **error**类型是内置包中的一个**接口类型**，其中仅包含一个**返回string、名为Error()的函数**，任何实现了该Error()方法的类型，都满足了该接口。
* **自定义错误类型**并使其满足error接口，可以使得程序处理错误更加灵活。
* 自定义错误类型的**名字应该以“Error”结尾。**

# 自增自减

* **只有`i++`与`i--`**，并且**只能单独一行。**
* **没有`--i`与`++i`**。

# 类型转换

* 由于Go语言是**强类型语言**，所以变量的类型转换**必须强制执行**。

### 数字间的转换

* ```go
  	var f float64 = 365.341
  	var i int64 = int64(f) // float64转为int64，这会导致小数点后的数字被截去
  	f = float64(i) // int64转为float64
  ```

* **有符号数和无符号数之间**也需要进行强制类型转换，**不同大小的数**之间也需要强制类型转换。

### 字符串、字符的转换

* 转换语法与数字间转换一致

### 字符、字符串和数字间的转换

* 将数字转换为对应的**utf-8字符**：

  ```go
  	i := 65
  	fmt.Println(string(i))
  ```

  此时数字**必须能被转换为`code point`**，才能被转换为字符

* 将**数字**转换为对应**数字字符串**：

  ```go
  	i := 65
  	j := fmt.Sprint(i)
  	fmt.Println(j)
  	k := fmt.Sprintf("%d", i)
  	fmt.Println(k)
  ```

  或者使用**`strconv`**包中的**`Itoa()`**函数：[strconv.Itoa()](#数字转换为字符串)

* 将**数字字符串**转换为**数字**：

  使用strconv包中的**`Atoi()`**函数：[strconv.Atoi()](#字符串转换为数字)

### 布尔类型转换

* Go中通常**不允许将bool类型和其余类型进行转换**，比如：

  ```go
  string(false)
  int(true)
  bool(1)
  ```

  以上代码均会报错。同时，**Go中`0、1`不会被当作`false`和`true`。**

# fmt包

### Printf对齐文本

* `%-10v`表示在**右方**填充空格，直至整个输出长度（包含变量输出长度）为10。
* `%10v`表示在**左方**填充空格，直至整个输出长度（包含变量输出长度）为10。

### printf打印小数

* ```go
  	third := 7.0 / 3
  	fmt.Println(third)
  	fmt.Printf("%v\n", third)
  	fmt.Printf("%f\n", third)
  	fmt.Printf("%.3f\n", third) // 保留小数点后3位
  	fmt.Printf("%4.3f\n", third) // 保留小数点后3位，并且包含小数点整体长度一共四位，若结果长度过大，则不做修剪；若长度过小，则填充空格
  	fmt.Printf("%4.2f\n", third) // 
  	fmt.Printf("%4.1f\n", third) // 若为4，则在左侧填充空格使其长度为4；若为-4，则在右侧填充空格使其长度为4
  	fmt.Printf("%04.1f\n", third) // 用0填充而不是空格
  ```

  上述代码打印结果：

  ```go
  2.3333333333333335
  2.3333333333333335
  2.333333
  2.333
  2.333
  2.33
   2.3
  02.3
  ```


### Printf打印数字类型

* ```go
  fmt.Printf("%T", i)
  ```

### Printf打印十六进制数

* ```go
  fmt.Printf("%x", i)
  ```

### Printf打印二进制数

* ```go
  fmt.Printf("%b", i)
  ```

# math包

* 该包中涉及的所有`float`数据都是`float64`，而不是`float32`，所以应当首选使用`float64`。

### rand包

* 返回$[0, n)$内的一个伪随机整数：

  ```go
  var tem = rand.Intn(10)
  ```

### big包

* 获得一个`big.Int`类型的数，其表示范围要大于`uint64`：

  ```go
  	i := big.NewInt(1)
  	fmt.Println(i)
  ```

  `big.NewInt()`函数作用为：**将一个`uint64`类型的数转换为`big.Int`类型的数。**

* 如果某些时候数的大小已经超过了`uint64`的上限，便不能使用上一种方法，只能：

  ```go
  	i := new(big.Int)
  	i.SetString("2400000000000000000", 10)
  	fmt.Println(i)
  ```

  先创建一个`big.Int`类型的实例，使用实例方法直接赋值。

* **注意：虽然Go编译器使用big包来处理无类型的数值常量，但是常量和big.Int类型之间是不能互换的。**

# strings包

* 判断字符串`str`是否包含字符串`“Hello”`：

  ```go
  iscontain := strings.Contains(str, "Hello")
  ```


# unicode包

### utf8包

* **`RuneCountInString()`**：返回一个字符串中**rune**字符长度：

  ```go
  	str := "Hello World"
  	fmt.Println(utf8.RuneCountInString(str))
  ```

* **`DecodeRuneInString()`**：返回字符串中**第一个字符和其所占空间大小**：

  ```go
  	str := "Hello World"
  	c, i := utf8.DecodeRuneInString(str)
  	fmt.Printf("%c, %d", c, i)
  ```

# strconv包

* 使用`strconv.Itoa()`将**数字**转换为对应的**数字字符串**（不是数字对应的字符）：<span id="数字转换为字符串"> </span>

  ```go
  	i := 65
  	j := strconv.Itoa(i)
  ```

* 使用`strconv.Atoi()`将**数字字符串**转换为对应**数字**：<span id="字符串转换为数字"> </span>

  ```go
  	i := "65"
  	j, err := strconv.Atoi(i)
  	if err != nil {
  		fmt.Println("there is an err")
  	}
  	fmt.Println(j)
  ```

  注意：如果字符串中**包含非数字字符**或者**字符串对应数字过大**，使用Atoi()函数**第一个返回值将为0，第二个返回值将非空**。

# sort包

* 使用`sort.Ints()`对int类型切片进行排序：

  ```go
  	s := []int{1, 21, 546, 1, 23}
  	sort.Ints(s)
  ```

# encoding包

### json包

* 使用`json.Marshal()`函数将**结构体**转为对应的**json格式**：

  ```go
  type point struct {
  	I int
  	J int
  }
  
  func main() {
  	p := point{I: 1, J: 2}
  	bytes, err := json.Marshal(p)
  	if err != nil {
  		os.Exit(1)
  	}
  	fmt.Println(bytes)
  	fmt.Println(string(bytes))
  }
  ```

# errors包

* 使用`New()`函数构造一个**error类型的错误**：

  ```go
  	err := errors.New("this is an error")
  	fmt.Printf("%v", err)
  ```

# sync包

* 使用**`sync`**包实现互斥锁：

  ```go
  	mutex := sync.Mutex{}
  	mutex.Lock()
  	defer mutex.Unlock()
  ```

# 函数

### 可变参数函数

* 某些函数可以接受**任意个某类型的参数**，然后将它们**“组合”**起来，以一个**切片类型的形参**传入函数：

  ```go
  func myfunc(prefix string, words ...string) {
  	for _, str := range words {
  		fmt.Println(prefix + str)
  	}
  }
  ```

  上述代码中，形参`words`的类型是**`[]string`**，是一个**切片**
  
* 在调用可变参数函数时，可以传入任意个参数，也可以传入一个**切片的“展开”**：

  ```go
  func main() {
  	myfunc("666", "i", "you")
  	s1 := []string{"i", "you"}
  	myfunc("666", s1...)
  }
  ```

  `s1...`可以被理解为`s1`切片的**“展开”**
  
  但是值得注意的是：如果在函数内**对`words`切片进行修改**，那么在函数外的**`s1`切片也会被修改**。
  
* 举一个例子，`fmt.Println()`函数的定义如下：

  ```go
  func Println(a ...interface{}) (n int, err error) {
  	return Fprintln(os.Stdout, a...)
  }
  ```

  其中：

  * `...`表示数量不限
  * `interface{}`表示参数`a`的类型是**空接口**，而**Go中所有类型相当于都实现了空接口**

  以上两点结合起来形成的效果就是：**`Println()`函数接收任意数量、任意类型的参数**

### 将函数赋值给变量

* **函数可以赋值给变量**，但是**不能加`()`**，否则就是**将函数执行的返回值赋值给变量**：

  ```go
  type myint int
  
  func func_1() myint {
  	fmt.Println("1")
  	return 1
  }
  
  func func_2() myint {
  	fmt.Println("2")
  	return 2
  }
  func main() {
  
  	myfunc := func_1 // 此时myfunc变量的类型为：func() myint
  	myfunc()
  
  	myfunc = func_2
  	myfunc()
  }
  ```

  此时myfunc变量的类型是**函数类型**，**执行该变量**就是执行背后的那个函数。
  
* 函数变量的**零值是 `nil`**，这意味着它**可以跟 `nil` 比较**，但两个函数变量之间不能比较。

### 将函数作为参数传递给函数

* **函数可以作为参数传递给其他函数**，此时该函数便就是**回调函数**：

  ```go
  func add(a, b int) int {
  	return a + b
  }
  
  func op(myfunc func(a, b int) int, a, b, c int) int {
  	return c - myfunc(a, b)
  }
  
  func main() {
  	fmt.Println(op(add, 1, 3, 10))
  }
  ```
  
* 若将函数A作为参数传递给函数B，那么需要在函数B的函数体内，**对A是否为`nil`进行判断**，若为`nil`，需要定义一些**默认行为。**

### 匿名函数

* 可以将匿名函数直接赋值给变量：

  ```go
  	var f = func(a, b int) int {
  		return a + b
  	}
  	//或者
  	f := func(a, b int) int {
  		return a + b
  	}
  ```

* 匿名函数也可以**立即执行**（或者称**“立即执行的函数表达式”**）：

  ```go
  	func(a, b int) int {
  		return a + b
  	}(1, 2)
  ```

### 匿名函数闭包

* 参考网站：
  * https://segmentfault.com/a/1190000022798222
  * 菜鸟：https://www.runoob.com/go/go-function-closures.html

* 匿名函数可以**保持着对上层作用域内部变量的引用**，使得那些变量**逃逸**（生命周期**没有随着作用域结束而结束**）：

  ```go
  func incr() func() int {
  	var x int
  	return func() int {
  		x++
  		return x
  	}
  }
  
  func main() {
  	i := incr()
  
  	println(i()) // 1
  	println(i()) // 2
  	println(i()) // 3
  
  	println(incr()()) // 1
  	println(incr()()) // 1
  	println(incr()()) // 1
  }
  ```

  上述代码中，返回的函数`i`依旧**保留着`incr()`中变量`x`的引用**，所以连续执行`i()`访问的是**同一个变量`x`**；但是连续执行`incr()`，**各自返回的函数保持的是不同的变量`x`的引用**。

* 阅读下面代码：

  ```go
  	var funcSlice []func()
  	for i := 0; i < 3; i++ {
  		func(i int) {
  			funcSlice = append(funcSlice, func() {
  				println(i)
  			})
  		}(i)
  	}
  
  	for j := 0; j < 3; j++ {
  		funcSlice[j]() // 0, 1, 2
  	}
  ```

  上述代码中，为`funcSlice`这个数组添加`func()`元素，每一个元素是一个打印变量`i`的函数，但是由于数组中每一个函数中的`i`是由**参数传递**（由于参数是**按值传递**，所以**每一个传递的`i`都是`for`循环中循环变量`i`的副本**）而来的，所以**各个匿名函数中引用的变量`i`其实是不同的变量。**

  ```go
  	var funcSlice []func()
  	for i := 0; i < 3; i++ {
      	funcSlice = append(funcSlice, func() {
          	println(i)
      	})
  
  	}
  	for j := 0; j < 3; j++ {
      	funcSlice[j]() // 3, 3, 3
  	}
  ```

  而这段代码中，**每一个匿名函数引用的其实都是用一个`i`，即`for`循环中的那个循环变量`i`。**

# 面向对象思想实现

### 方法的关联

* 在C++、Java等面向对象语言中，方法属于类，通过类来使用方法。但在Go中提供了方法，但**没提供类和对象**，仅仅允许**将变量和方法关联起来，从而变为更加灵活。**

  ```go
  func fun(k myint) myint {
  	return k
  }
  
  func (k myint) fun_1() myint {
  	return k
  }
  ```

* 未和变量关联的函数可以有多个参数，但是和变量关联的方法有且仅有一个**接收者**。

### 将方法和结构关联

* **将方法和结构体进行关联**：

  ```go
  type point struct {
  	I int
  	J int
  }
  
  func (p point) print() {
  	fmt.Println(p.I)
  	fmt.Println(p.J)
  }
  
  func main() {
  	p1 := point{1, 2}
  	p2 := point{3, 4}
  	p1.print()
  	p2.print()
  }
  ```

### 构造函数

* Go语言中**没有特定的构造函数**，但是可以实现一些**“用于构造结构体的函数”**，这些函数便为“构造函数”：

  ```go
  type point struct {
  	I int
  	J int
  }
  func NewPoint(i int, j int) point {
  	return point{i, j}
  }
  
  func main() {
  	p1 := NewPoint(1, 2)
  	p2 := NewPoint(3, 4)
  }
  ```

  “构造函数”的名字一般**以“new”或者“New”开头**

### 嵌入类型

* Go语言中**没有类、继承等概念**，但是可以通过**组合、嵌入等方式可以实现继承效果**：

  ```go
  type dad struct {
  	age    int
  	weight float64
  }
  type mom struct {
  	age    int
  	weight float64
  }
  
  type school struct {
  	address string
  	name    string
  }
  type son struct {
  	dad
  	mom
  	school school
  }
  ```

  以上代码中，`dad`和`mom`为内部类型，`son`为外部类型。

# 数组

### 数组类型

* 数组类型由**数组长度**和**元素类型**所组成（长度和元素类型都是数组类型的**一部分**），所以不同长度的数组或者不同元素类型的数组之间**属于不同类型**，尽管它们都是数组。

### 复合字面值

* 一种给复合类型初始化的紧凑语法：

  ```go
  	arr := [3]int{1, 2, 3}
  	fmt.Println(len(arr))
  
  	arr := [...]int{1, 2, 3} // ...表示长度未定，编译器自动计算出长度
  	fmt.Println(len(arr))
  ```

### 数组的拷贝

* Go中数组也是一种**值**，可以**被赋值给新的变量**或者**传递给函数当作参数**，但这些过程中会产生一个完整的**数组副本（因为数组是一个值）**。这不同于某些面向对象语言（如Java），在那些语言中，数组可能是**一种对象**，将对象赋值给其他变量或者传递给函数当参数时，**传递的是引用，而不是值**，所以不会产生数组副本。

# 切片（slice）

### 切片的实质

* 一个切片实质上是对一个**底层数组**切分的**窗口（或者视图）**。

* 切片也有类型，只不过**不包括切片长度，只有元素类型**（不同于数组）。
* 也可以对字符串`string`进行切分，不过**切分的索引是字节数，而不是字符数**。

### 声明切片方式

* 直接**对一个数组进行切分**：

  ```go
  	arr := [...]int{1, 2, 3, 4, 5, 6}
  	s1 := arr[0:3]
  ```

  若**省略掉起始和结束索引**，那么返回的就是一个**包含数组所有元素的切片**：

  ```go
  	arr := [...]int{1, 2, 3, 4, 5, 6}
  	s1 := arr[:]
  ```

* 使用**复合字面值**：

  ```go
  	arr := []int{1, 2, 3}
  ```

  由于**切片的底层存在着一个数组**，所以直接声明切片时，**Go会先创建一个底层数组，再在该数组基础上创建切片。**

* 使用**`make()`**函数：

  ```go
  	s1 := make([]int, 5, 10)
  ```

* 使用**`new()`**：

  ```go
  	s1 := *new([]int)
  ```

### 长度和容量

* 使用**cap()**内置函数获得容量，使用**len()**内置函数获得长度：

  ```go
  	arr := [...]int{1, 2, 3, 4, 5, 6}
  	s1 := arr[0:]
  	s2 := append(s1, 7)
  	fmt.Println(cap(s1))
  	fmt.Println(cap(s2))
  ```

# Map

### map的声明

* `var`声明：

  ```go
  	var m map[int]string
  ```

* 使用复合字面值：

  ```go
  	m := map[int]string{
  		1: "1",
  		2: "2",
  	}
  ```

* 使用**`make()`**函数：

  ```go
  	m := make(map[int]string, 10)
  	fmt.Println(len(m))
  ```

  第二个参数为**可选参数**，可以**为指定数量的key预分配空间**

### 向map添加和修改元素

* 直接使用键值对操作便可添加或修改元素：

  ```go
  	m[1] = "1"
  	m[3] = "3"
  ```

  原map中**没有3这个键**，那么该行代码将在map中**新添加一个键值对  `3:"3"`**

### 删除元素

* 使用`delete()`函数删除键值对：

  ```go
  	delete(m, 1)
  ```

### map的使用

* 若使用map返回一个**map中不存在的键**对应的值，那么返回的结果将是**值类型**对应的**空值**：

  ```go
  	fmt.Println(m[4])
  ```

  原map中**没有4这个键**，那么上述代码将打印一行**空字符串**`""`，因为map的值类型是`string`

# 结构struct

### struct的声明

* 使用`var`关键字声明局部struct：

  ```go
  	var point struct {
  		i int
  		j int
  	}
  	point.i = 1
  	point.j = 2
  ```

* 通过**类型**复用结构体：

  ```go
  type point struct {
  	i int
  	j int
  }
  
  func main() {
  	var p point
  	//p := point{}
  	p.i = 1
  	p.j = 2
  }
  ```

* 使用**复合字面值**：

  ```go
  type point struct {
  	i int
  	j int
  }
  
  func main() {
  	p := point{i: 1, j: 2}
  	m := point{1, 2}
  }
  ```

  第一种为使用**成对的值和字段**，进行初始化。这种初始化方式**不需要按照结构变量顺序**进行初始化，并且也**不要求将结构体内所有变量都进行初始化**。所以这种方式可以容忍结构体内成员变量的变化（如增加了数个成员变量）。

  第二种就是按**字段定义的顺序**进行初始化。

# 接口

### 接口的声明

* 使用`var`关键字：

  ```go
  type dad struct {
  	age int
  }
  
  func (d dad) talk() string {
  	return "666"
  }
  
  func main() {
  	var person interface {
  		talk() string
  	} = dad{age: 10}
  	fmt.Println(person.talk())
  }
  ```

* 使用**类型**复用接口：

  ```go
  type person interface {
  	talk() string
  }
  type dad struct {
  	age int
  }
  
  func (d dad) talk() string {
  	return "666"
  }
  ```


### 类型断言

* 使用类型断言可以判断**一个接口`i`是否为`nil`**，并且判断**其存储的值的类型是否为`T`类型**：

  ```go
  t := i.(T)
  ```

  若断言成功，则将`T`类型的值返回给`t`；若断言失败，则**触发`panic`**

  ```go
  t, ok := i.(T)
  ```

  若断言成功，则将`T`类型的值返回给`t`，并且`ok`为`true`；若断言失败，`ok`为`false`，`t`为`T`类型的零值，不会触发`panic`

# 并发

### goroutine

* 执行goroutine，只需在调用前添加`go`关键字即可。

### 通道

* 使用make()函数初始化通道：

  ```go
  	c := make(chan int)
  ```

* 向通道传输值：

  ```go
  	c <- 99
  ```

* 从通道接受值：

  ```go
  	i := <-c
  ```

### 多个通道

* 使用`time.After`创建一个**超时通道**，它将**在规定时间内返回一个值**，常用于防止goroutine一直等待通道数据而阻塞：

  ```go
  	timeoutChan := time.After(2 * time.Second)
  ```

* 使用`select`语句处理多个通道：

  ```go
  	c := make(chan int)
  	timeoutChan := time.After(2 * time.Second)
  
  	select {
  	case Data := <-c:
  		fmt.Println(Data)
  	case <- timeoutChan:
  		fmt.Println("my patience ran out")
  	}
  ```

* 关闭通道并且判断通道是否已经关闭：

  ```go
  	close(c)
  	data, ok := <-c
  ```

  若`ok`为`false`，那么表明该通道已经关闭，若尝试**读取一个已经关闭的通道**，会得到一个**对应类型的零值**。

### 常用模式

* 使用`range`关键字**不断从通道读取数据，直到通道被关闭：**

  ```go
  	c := make(chan int)
  	for data := range c {
  		fmt.Println(data)
  	}
  ```

* 使用**`for`+`select`**实现从多个通道读取数据，并实现**轮询**：

  ```go
  	c := make(chan int)
  	timeoutChan := time.After(2 * time.Second)
  
  	for {
  		select {
  		case data := <-c:
  			fmt.Println(data)
  		case <-timeoutChan:
  			fmt.Println("ran out")
  			return
  		default:
  			fmt.Println("go on")
  		}
  		// Todo else
  	}
  ```

### 互斥

* 使用**`sync`**包实现互斥锁：

  ```go
  	mutex := sync.Mutex{}
  	mutex.Lock()
  	defer mutex.Unlock()
  ```

  
