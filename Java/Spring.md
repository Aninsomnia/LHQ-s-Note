#### IOC（控制反转）

* 一个理论、概念、思想，描述的是把对象的创建、赋值、管理工作交给代码之外的容器实现，由其他的外部资源实现。
* 控制：创建对象，对象的属性赋值，对象之间的关系的管理。
* 反转：把原来的开发人员管理创建对象的权限转移给代码之外的容器实现，由容器代替开发人员。
* 正转：由开发人员在代码中使用new创建对象，开发人员主动管理对象。
* **控制反转是把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓的"控制反转"就是对组件对象控制权的转移，从程序代码本身转移到了外部容器，由容器来创建对象并管理对象之间的依赖关系。**
* **所有的类的创建、销毁都由 spring来控制，也就是说控制对象生存周期的不再是引用它的对象，而是spring。**
* 目的：减少对代码的改动，也能实现不同的功能，实现解耦合，**由IoC容器帮对象找相应的依赖对象并注入，而不是由对象主动去找。**。
* DI（依赖注入）：IOC的实现
  * **只需要在程序中提供要使用的对象名称，至于对象的创建、赋值、查找都由容器实现。**

#### 创建对象

* 创建maven项目
* 加入maven依赖
* 创建类
* 创建spring的配置文件

#### 配置文件

* beans是根标签，表示众多对象，每一个对象对应一个bean
* xsd是一种约束文件类型
* bean标签：<bean id = "" class = "" />
  * id是对象的自定义名称唯一值。Spring通过这个名称来找到对象
  * class是类的全限定名称（不能是接口，因为Spring是反射机制，必须使用类）
* Spring是把创建好的对象放在map中。SpringMap.put(id的值, 对象)；
* 通过bean标签声明对象相当于在Spring内部用new方法创建了一个对象。

#### bean的作用域

* **singleton：单例模式。**即在整个 Spring 容器中，使用 singleton 定义的 Bean 将是单例 的，叫这个名称的对象只有一个实例。默认为单例的。
* **prototype：原型模式。**即每次使用 getBean 方法获取的同一个的实例都是一个新的实例。
* **request：**对于每次 HTTP 请求，都将会产生一个不同的 Bean 实例。 
* **session：**对于每个不同的 HTTP session，都将产生一个不同的 Bean 实例。

#### 测试文件

* **指定spring配置文件名称：**

  ```java
  String config = "beans.xml"
  ```

* **创建Spring容器**：ApplicationContext，通过这个容器就可以获取对象，创建时会读取配置文件中<bean/>标签中的信息并创建对象。

  * **注意：容器中的对象在创建容器对象时就被创建，而且容器配置文件中所有对象都会被创建，默认调用无参构造方法。**

  * ```java
    ApplicationContext ctx = new ClassPathXmlApplicationContext(config)
    ```

  * ```java
    //ClassPathXmlApplicationContext：表示从类路径中加载Spring的配置文件
    ```

* **获取对象**：通过getbean()：getbean (xml文件中对象的id)，返回一个object对象

  * ```java
    SomeService service = (SomeService) ctx.getBean("someService");
    ```

#### 创建一个非自定义类

* 将class中的内容为该类在java中的位置就可。

#### 给一个对象属性赋值

* DI（依赖注入）：创建对象，给属性赋值，实现方式有两种
  * 在配置文件中，使用标签和属性完成：**基于XML的di实现**
  * 使用Spring中的注解：**基于注解的DI实现**
*  DI的分类：
  * **Set注入（设值注入）：Spring调用类的set方法，在set方法中实现属性的赋值**，在bean标签中加入property标签，一个property标签只能给一个属性赋值
    
    * 简单类型的set注入：<property name="name" value="李四" />
    
    * 引用类型的Set注入：<property name="school" ref="mySchool" />
    
      或者：						
    
      <property name="school">
          <ref bean="mySchool"/>
      </property>
    
    * 注意：set注入只是Spring调用set方法，而set方法中的程序内容由我们程序员决定。
  * **构造注入：Spring调用类的有参数构造方法，在构造方法中完成赋值**

#### AOP（面向切面编程）

* AOP 是一种编程思想，是面向对象编程（OOP）的一种补充。面向对象编程将程序抽象成各个层次的对象，而面向切面编程是将程序抽象成各个切面。
* 