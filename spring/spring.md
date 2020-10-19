# spring

此处的spring实际上是springframework

框架”春天”  ==核心技术是IOC和AOP==.

Spring是一个容器,容器中存放的是JAVA对象

使用目的:解耦合,将项目中使用的对象放入容器中,让容器来完成对象的创建和关系的管理(赋值)

![image-20201012151052956](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201012151052956.png)



# IOC

> IoC (Inversion of Control) : 控制反转， 是一个理论，概念，思想。
> 描述的：把对象的创建，赋值，管理工作都交给代码之外的容器实现， 也就是对象的创建是有其它外部资源完成。
>
> 控制： 创建对象，对象的属性赋值，对象之间的关系管理。
>
> 反转： 把原来的开发人员管理，创建对象的权限转移给代码之外的容器实现。 由容器代替开发人员管理对象。创建对象，给属性赋值。
>
> 正转：由开发人员在代码中，使用new 构造方法创建对象， 开发人员主动管理对象。
>     public static void main(String args[]){
>          Student student = new Student(); // 在代码中， 创建对象。--正转。
>
> 为什么要使用 ioc ： 目的就是减少对代码的改动， 也能实现不同的功能。 实现解耦合。 
>
> IOC是spring实现的核心,能够实现业务对象之间的解耦合,例如service和dao对象之间的解耦合

DI 是ioc的技术实现.

 DI（Dependency Injection） :依赖注入， 只需要在程序中提供要使用的对象名称就可以， 至于对象如何在容器中创建，赋值，查找都由容器内部实现。

spring是使用的di实现了ioc的功能， spring底层创建对象，使用的是反射机制。

## 什么时候使用注解什么时候使用配置文件?

需要经常修改的就使用xml,不经常修改的则使用注解 .一般以注解为主,配置文件为辅助的实际项目开发

xml的优缺点:与代码分开,几乎不影响源代码 / 代码量多,效率较低

注解:使用方便效率高 ,可读性高/嵌入在源代码中,使得源代码乱杂

## IOC-基于XML的DI

### 通过XML将对象放置于容器中的实现步骤:

```java
1.加入maven依赖
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>5.2.5.RELEASE</version>
</dependency>
  
2.配置spring的xml				
resources目录创建一个xml文件，文件名可以随意,但Spring建议的名称为 applicationContext.xml。

//spring的xml文件idea自带有模板

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>

//spring-beans.xsd 是约束文件，和mybatis指定  dtd是一样的。
//定义<bean>标签来定义实例对象
<bean id="" class="" />
//id:对象的自定义名称，唯一值。 spring通过这个名称找到对象
// class:类的全限定名称（因为spring是反射机制创建对象，必须使用类,不能是接口,注意,可以创建非自定义对象,也就是使用Date之类的类对象）  

3.创建容器,并获取对象
String config="spring.xml";
ApplicationContext ac = new ClassPathXmlApplicationContext(config);
Object object =ac.getBean("id"); //获取对象
ac.getBeanDefinitionCount(); //获取容器中定义的对象的数量
ac.getBeanDefinitionNames();	//容器中每个定义的对象的名称,即id
  
//ApplicationContext用于加载Spring的配置文件在程序中充当“容器”的角色,就是表示Spring容器,其实现类有两个。
//ClassPathXmlApplicationContext:表示从类路径中加载spring的配置文件
//FileSystemXmlApplicationContext用于加载项目外的xml的,一般不使用
//spring默认创建对象的时间：在创建spring的容器时，会创建配置文件中的所有的对象。
//spring创建对象：默认调用的是无参数构造方法
```

![image-20201012224702149](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201012224702149.png)

### DI-set注入

又叫设值注入,注入就是赋值的意思, di:给属性赋值

```java
简单类型： spring中规定java的基本数据类型和String都是简单类型。
       
1.set注入（设值注入） ：
spring调用类的set方法， 你可以在set方法中完成属性赋值   
         1）简单类型的set注入  
            <bean id="xx" class="yyy">
               <property name="属性名" value="值"/>
               一个property只能给一个属性赋值
               <property....>
            </bean>

         2) 引用类型的set注入 ： spring调用类的set方法
           <bean id="xxx" class="yyy">
              <property name="属性名称" ref="bean的id(对象的名称)" />
           </bean>

       

//注意,set注入为调用set方法,也就是说,即使这个类本身没有这个属性,但是拥有setXXX方法,依然可以设置 <property....>来调用该方法并往该方法传入值
```

### DI-构造注入

```java
2.构造注入：
spring调用类有参数构造方法，在创建对象的同时，在构造方法中给属性赋值。

构造注入使用 <constructor-arg> 标签
一个<constructor-arg>表示构造方法一个参数。
标签属性：
name:表示构造方法的形参名
index:表示构造方法的参数的位置，参数从左往右位置是 0 ， 1 ，2的顺序
             
value：构造方法的形参类型是简单类型的，使用value
ref：构造方法的形参类型是引用类型的，使用ref
//省略index写法,标签只写value值,标签顺序为形参顺序
//可使用构造注入调用File类的构造函数创建文件
```

### DI-自动注入

```java
引用类型的自动注入： 
spring框架根据某些规则可以给引用类型赋值。·不用你在给引用类型赋值了
       
使用的规则常用的是byName, byType.
1.byName(按名称注入) ： 
java类中引用类型的属性名和spring容器中（配置文件）<bean>的id名称一样，且数据类型是一致的，这样的容器中的bean，spring能够赋值给引用类型。
         语法：
         <bean id="xx" class="yyy" autowire="byName">
            简单类型属性赋值
         </bean>

2.byType(按类型注入) ： 
java类中引用类型的数据类型和spring容器中（配置文件）<bean>的class属性是同源关系的，这样的bean能够赋值给引用类型
         同源就是一类的意思：
          1.java类中引用类型的数据类型和bean的class的值是一样的。
          2.java类中引用类型的数据类型和bean的class的值父子类关系的。
          3.java类中引用类型的数据类型和bean的class的值接口和实现类关系的
         语法：
         <bean id="xx" class="yyy" autowire="byType">
            简单类型属性赋值
         </bean>
//注意：在byType中， 在xml配置文件中只能有一个符合条件的bean，多一个会出现异常错误
//使用autowire属性后,会根据值的不同根据不同规则自动去寻找符合规则的bean作为使用该属性的bean对象的引用形参的值
```

### 包含关系的配置文件

 为什么使用?体积比只使用一个配置文件小,读取更快,效率更高.开发时由多人共同编辑配置文件,将配置文件分为多个可以避免一些出错

```java
包含关系的配置文件：
         主配置文件 ：包含其他的配置文件的，主配置文件一般是不定义对象的。
         语法：<import resource="其他配置文件的路径" />
         关键字："classpath:" 表示类路径（class文件所在的目录），
               在spring的配置文件中要指定其他文件的位置， 需要使用classpath，告诉spring到哪去加载读取文件。

		<!--加载的是文件列表-->
    <!--
    <import resource="classpath:ba06/spring-school.xml" />
    <import resource="classpath:ba06/spring-student.xml" />
    -->

    <!--
       在包含关系的配置文件中，可以通配符（*：表示任意字符）
       注意： 主的配置文件名称不能包含在通配符的范围内（不能叫做spring-total.xml）
    -->
   //注意:若想使用通配符,这些配置文件必须放在同一个包中才能使用(源代码造成)
```

## IOC-基于注解的DI

###  使用注解的步骤：

1.加入maven的依赖 spring-context ，在你加入spring-context的同时， 间接加入spring-aop的依赖。
	  

​	使用注解必须使用spring-aop依赖

2.在类中加入spring的注解（多个不同功能的注解）

 	1.@Component
	 2.@Respotory
	 3.@Service
	 4.@Controller
	 5.@Value
	 6.@Autowired
	 7.@Resource

3.在spring的配置文件中，加入一个组件扫描器的标签，说明注解在你的项目中的位置

4.创建容器ApplicationContext,并获取对象getbean()

### 创建对象注解

```java
* @Component: 创建对象的， 等同于<bean>的功能
 *     属性：value 就是对象的名称，也就是bean的id值，
 *          value的值是唯一的，创建的对象在整个spring容器中就一个
 *     位置：在类的上面
 *
 *  @Component(value = "myStudent")等同于
 *   <bean id="myStudent" class="com.bjpowernode.ba01.Student" />
 *
 *  spring中和@Component功能一致，创建对象的注解还有：
 *  1.@Repository（用在持久层类的上面） : 放在dao的实现类上面，
 *               表示创建dao对象，dao对象是能访问数据库的。
 *  2.@Service(用在业务层类的上面)：放在service的实现类上面，
 *              创建service对象，service对象是做业务处理，可以有事务等功能的。
 *  3.@Controller(用在控制器的上面)：放在控制器（处理器）类的上面，创建控制器对象的，
 *              控制器对象，能够接受用户提交的参数，显示请求的处理结果。
 *  以上三个注解的使用语法和@Component一样的。 都能创建对象，但是这三个注解还有额外的功能。
 *  @Repository，@Service，@Controller是给项目的对象分层的。
 *
 *@Component用于非以上三类的对象创建,不一定是实体类对象
 */
//使用value属性，指定对象名称
//@Component(value = "myStudent")

//省略value
@Component("myStudent")   最常用,类似的都可以省略value

//不指定对象名称，由spring提供默认名称: 类名的首字母小写
//@Component 即对象名为student
```

### 赋值注解

```java
* @Value: 简单类型的属性赋值
     *   属性： value 是String类型的，表示简单类型的属性值
     *   位置： 1.在属性定义的上面，无需set方法，推荐使用。
     *         2.在set方法的上面
  //@Value(value="李四" ) 或者 @Value("李四") (常用)
```

```java
* 引用类型
     * @Autowired: spring框架提供的注解，实现引用类型的赋值。
     * spring中通过注解给引用类型赋值，使用的是自动注入原理 ，支持byName, byType
     * @Autowired:默认使用的是byType自动注入。
     *
     *  位置：1）在属性定义的上面，无需set方法， 推荐使用
     *       2）在set方法的上面
       //注意,使用时容器中必须存在相应的引用类型对象才能赋值,也就是说使用了注解或XML生成了这个对象
       
使用@Autowired 的byName
     *  如果要使用byName方式，需要做的是：
     *  1.在属性上面加入@Autowired
     *  2.在属性上面加入@Qualifier(value="对象名") ：表示使用指定名称的bean完成赋值。
       //两个注解没有先后顺序,开发一般将Autowired写在Qualifier上面
       
@Autowired的属性required
 	   *  属性：required ，是一个boolean类型的，默认true
     *       required=true：表示引用类型赋值失败，程序报错，并终止执行。
     *       required=false：引用类型如果赋值失败， 程序正常执行，引用类型是null
       //开发中一般使用true,能尽早的将null问题暴露,防止之后调用时出现空指针异常
```

### JDK注解@Resource 自动注入

```java
 * 引用类型
 * @Resource: 来自jdk中的注解，spring框架提供了对这个注解的功能支持，可以使用它给引用类型赋值
 *            使用的也是自动注入原理，支持byName， byType .默认是byName
 *  位置： 1.在属性定义的上面，无需set方法，推荐使用。
 *        2.在set方法的上面
 */
//默认是byName： 先使用byName自动注入(使用该属性的声明名称)，如果byName赋值失败，再自动使用byType
 
* @Resource只使用byName方式，需要增加一个属性 name
* name的值是bean的id（名称）
    @Resource(name = "mySchool")
   //若直接声明name,则只使用byName方式
```

### 声明组件扫描器

component-scan工作方式： 

spring会扫描遍历base-package指定的包，把包中和子包中的所有类，找到类中的注解，按照注解的功能创建对象，或给属性赋值。

加入了component-scan标签，配置文件的变化：

1.加入一个新的约束文件spring-context.xsd
2.给这个新的约束文件起个命名空间的名称

```xml
 <context:component-scan base-package="" />  //component 组件   scan 扫描
声明组件扫描器(component-scan),组件就是java对象
属性: base-package：指定注解在你的项目中的包名。
**指定多个包的三种方式**:

1.使用多次组件扫描器，指定不同的包 

2.使用分隔符（;或,）分隔多个包名 

3.指定父包
```

### 注解与properties配置文件使用

```java
1.将属性名和属性值以K,V的形式写入properties文件中
2.<!--加载属性配置文件--> <context:property-placeholder location="classpath:XXX.properties" />
   加载包               <context:component-scan base-package="XXX" />
3.注解以@Value("${myname}")类似的形式读取properties
```

# 	AOP-面向切面编程

![image-20201014114710807](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014114710807.png)

## 是什么,有什么用:

AOP就是规范化的动态代理， 把动态代理的实现步骤，方式都定义好了， 
让开发人员用一种统一的方式，使用动态代理。

AOP 底层，就是采用动态代理模式实现的。采用了两种代理：JDK 的动态代理，与 CGLIB 的动态代理。

AOP（Aspect Orient Programming）面向切面编程

Aspect: 切面，给你的目标类增加的功能，就是切面。 像上面用的日志，事务都是切面。切面的特点： 一般都是非业务方法，独立使用的。

Orient：面向， 对着。

Programming：编程

oop: 面向对象编程

**1.动态代理**
**实现方式：**

**jdk动态代理，使用jdk中的Proxy，Method，InvocaitonHanderl创建代理对象。**
          **jdk动态代理要求目标类必须实现接口**

**cglib动态代理：第三方的工具库，创建代理对象，原理是继承。 通过继承目标类，创建子类.子类就是代理对象。 要求目标类不能是final的， 方法也不能是final的**

**2.动态代理的作用：**(同时也就是AOP的作用)

**1）在目标类源代码不改变的情况下，增加功能。**
	

**2）减少代码的重复**
	

**3）专注业务逻辑代码**
	

**4）解耦合，让你的业务功能和日志，事务非业务功能分离。**

> CGLIB优势:运行效率高,对类的要求低

**怎么理解面向切面编程 ？** 

**1）需要在分析项目功能时，找出切面。**

**2）合理的安排切面的执行时间（在目标方法前， 还是目标方法后）**

**3）合理的安排切面执行的位置，在哪个类，哪个方法增加增强功能**

术语：

1）Aspect:切面，表示增强的功能， 就是一堆代码，完成某个一个功能。非业务功能， 常见的切面功能有日志， 事务， 统计信息， 参数检查， 权限验证。

2）JoinPoint:连接点 ，连接业务方法和切面的位置。 就某类中的业务方法
	

3）Pointcut : 切入点 ，指多个连接点方法的集合。多个方法
	

4）目标对象： 给哪个类的方法增加功能， 这个类就是目标对象
	

5）Advice:通知，通知表示切面功能执行的时间。

==说一个切面有三个关键的要素：==

1）切面的功能代码，切面干什么

2）切面的执行位置，使用Pointcut表示切面执行的位置

3）切面的执行时间，使用Advice表示时间，在目标方法之前，还是目标方法之后。

## 什么时候用:

![image-20201014150710315](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014150710315.png)

spring框架采用接口来实现AOP,我们项目开发中很少使用spring的aop实现。 因为spring的aop比较笨重。

一般使用aspectJ来实现aop?

## 使用aspectj实现aop的基本步骤：

aop的技术实现框架：
	

1.spring：spring在内部实现了aop规范，能做aop的工作。spring主要在事务处理时使用aop。 我们项目开发中很少使用spring的aop实现。 因为spring的aop比较笨重。

 2.aspectJ: 一个开源的专门做aop的框架。spring框架中集成了aspectj框架，通过spring就能使用aspectj的功能。
	  

aspectJ框架实现aop有两种方式：
	   		

1).使用xml的配置文件 ： 配置全局事务
		

==2).使用注解，我们在项目中要做aop功能，一般都使用注解==

 6.学习aspectj框架的使用。 

①==表示切面的执行时间==， 这个执行时间在规范中叫做Advice(通知，增强)
	  

 在aspectj框架中使用注解表示的。也可以使用xml配置文件中的标签表示,但是一般使用注解,XML只用于配置事务	

1）@Before
		2）@AfterReturning
		3）@Around
		4）@AfterThrowing
		5）@After

②==表示切面执行的位置==，使用的是切入点表达式。execution(访问权限 ==方法返回值==  ==方发声明(参数)==  异常类型)

![image-20201013151508230](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201013151508230.png)

​	![image-20201013151155073](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201013151155073.png)

```java
1.新建maven项目
2.加入依赖
  1）spring依赖
  2）aspectj依赖
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-aspects</artifactId>
	<version>5.2.5.RELEASE</version>
</dependency>
  3）junit单元测试
3.创建目标类：接口和他的实现类。
  要做的是给类中的方法增加功能

4.创建切面类：普通类
  1）在类的上面加入 @Aspect
  2）在类中定义方法， 方法就是切面要执行的功能代码
    在方法的上面加入aspectj中的通知注解，例如@Before 需要指定切入点表达式execution()

5.创建spring的配置文件：声明对象，把对象交给容器统一管理
  声明对象你可以使用注解或者xml配置文件<bean>
  1)声明目标对象
  2）声明切面类对象
  3）声明aspectj框架中的自动代理生成器标签。
     自动代理生成器：用来完成代理对象的自动创建功能的。

6.创建测试类，从spring容器中获取目标对象（实际就是代理对象）。
  通过代理执行方法，实现aop的功能增强。
  //直接通过getBean方法获取目标对象,此时获取的已经是代理对象,调用方法即可
  //若切入点写错,也就是该切面类无法找到目标类,也就不会存在代理对象,此时getBean获取的是普通的目标类
```

###  创建切面类

@Aspect

```java
*  @Aspect : 是aspectj框架中的注解。
*     作用：表示当前类是切面类。
*     切面类：是用来给业务方法增加功能的类，在这个类中有切面的功能代码
*     位置：在类定义的上面 
```

@before….

```java
* 定义方法，方法是实现切面功能的。
```

### 创建spring的配置文件

```xml
<!--声明目标对象-->
<bean id="someService" class="com.bjpowernode.ba08.SomeServiceImpl" />

<!--声明切面类对象-->
<bean id="myAspect" class="com.bjpowernode.ba08.MyAspect" />

<!--声明自动代理生成器：使用aspectj框架内部的功能，创建目标对象的代理对象。
    创建代理对象是在内存中实现的， 修改目标对象的内存中的结构。 创建为代理对象
    所以目标对象就是被修改后的代理对象.
 aspectj-autoproxy:会把spring容器中的所有的目标对象，一次性都生成代理对象。

<!--	目标类用接口时,默认使用的是jdk动态代理,没有时使用的是cglib动态代理
       如果你期望目标类有接口，使用cglib代理
       proxy-target-class="true":告诉框架，要使用cglib动态代理
    -->
    <aop:aspectj-autoproxy proxy-target-class="true"/>
```

## JionPoint

```java
* 指定通知方法中的参数 ： JoinPoint
* JoinPoint:业务方法，要加入切面功能的业务方法
*    作用是：可以在通知方法中获取方法执行时的信息， 例如方法名称，方法的实参。
*    如果你的切面功能中需要用到方法的信息，就加入JoinPoint.
*    这个JoinPoint参数的值是由框架赋予， 必须是第一个位置的参数
  public void myBefore(JoinPoint jp){
  "连接点方法的定义="+jp.getSignature()
  "连接点方法的名称="+jp.getSignature().getName()
   //获取方法的实参
   Object args [] = jp.getArgs();
}
```

## 通知注解

### 前置通知注解@before

```java
* 定义方法，方法是实现切面功能的。
     * 方法的定义要求：
     * 1.公共方法 public
     * 2.方法没有返回值
     * 3.方法名称自定义
     * 4.方法可以有参数，也可以没有参数。
   //  如果有参数，参数不是自定义的，有几个参数类型可以使用。例如JionPoint 

  * @Before: 前置通知注解
     *   属性：value ，是切入点表达式，表示切面的功能执行的位置。
     *   位置：在方法的上面
     * 特点：
     *  1.在目标方法之前先执行的
     *  2.不会改变目标方法的执行结果
     *  3.不会影响目标方法的执行。
    @Before(value = "execution(public void com.bjpowernode.ba01.SomeServiceImpl.doSome(String,Integer))")
    public void myBefore(){
        //就是你切面要执行的功能代码
        System.out.println("前置通知， 切面功能：在目标方法之前输出执行时间："+ new Date());
    }
//before->切面的执行时间  value->切面的执行位置  方法-->切面的功能代码
```

### 后置通知注解@AfterReturning

```java
* 4.方法有参数的,推荐是Object ，参数名自定义
   
  * @AfterReturning:后置通知
     *    属性：1.value 切入点表达式
     *         2.returning 自定义的变量，表示目标方法的返回值的。
     *          自定义变量名必须和通知方法的形参名一样。//returning = "res"    Object res
     *    位置：在方法定义的上面
     * 特点：
     *  1。在目标方法之后执行的。
     *  2. 能够获取到目标方法的返回值，可以根据这个返回值做不同的处理功能
     *      Object res = doOther();
     *  3. 可以修改这个返回值
     @AfterReturning(value = "execution(* *..SomeServiceImpl.doOther(..))",returning = "res")
    	public void myAfterReturing(  JoinPoint jp  ,Object res ){
```

### 环绕通知注解@Around

```java
 * 环绕通知方法的定义格式
     *  1.public
     *  2.必须有一个返回值，推荐使用Object
     *  3.方法名称自定义
     *  4.方法有参数，固定的参数 ProceedingJoinPoint
   
 * @Around: 环绕通知
     *    属性：value 切入点表达式
     *    位置：在方法的定义上面
     * 特点：
     *   1.它是功能最强的通知
     *   2.在目标方法的前和后都能增强功能。
     *   3.控制目标方法是否被调用执行   //ProceedingJoinPoint  extends JionPoint 可以获得目标方法获得的实参,从而设定要不要执行pjp.proceed()
     *   4.修改原来的目标方法的执行结果。 影响最后的调用结果
     *
     *  环绕通知，等同于jdk动态代理的，InvocationHandler接口
     *
     *  参数：  ProceedingJoinPoint 就等同于 Method
     *         作用：执行目标方法的
     *  返回值： 就是目标方法的执行结果，可以被修改。
     *
     *  环绕通知： 经常做事务， 在目标方法之前开启事务，执行目标方法， 在目标方法之后提交事务
		@Around(value = "execution(* *..SomeServiceImpl.doFirst(..))")
    public Object myAround(ProceedingJoinPoint pjp) throws Throwable {
   								pjp.proceed()  //目标方法的调用
```

### 异常通知@AfterThrowing(很少使用)

```java
 * 异常通知方法的定义格式
     *  1.public
     *  2.没有返回值
     *  3.方法名称自定义
     *  4.方法有个一个Exception， 如果还有是JoinPoint,

* @AfterThrowing:异常通知
     *     属性：1. value 切入点表达式
     *          2. throwinng 自定义的变量，表示目标方法抛出的异常对象。
     *             变量名必须和方法的参数名一样
     * 特点：
     *   1. 在目标方法抛出异常时执行的
     *   2. 可以做异常的监控程序， 监控目标方法执行时是不是有异常。
     *      如果有异常，可以发送邮件，短信进行通知
     *
     *  执行就是：
     *   try{
     *       SomeServiceImpl.doSecond(..)
     *   }catch(Exception e){
     *       myAfterThrowing(e);
     *   }
     */
    @AfterThrowing(value = "execution(* *..SomeServiceImpl.doSecond(..))",
            throwing = "ex")
    public void myAfterThrowing(Exception ex) {
        System.out.println("异常通知：方法发生异常时，执行："+ex.getMessage());
```

### 最终通知@After(很少用)

```java
 * 最终通知方法的定义格式
     *  1.public
     *  2.没有返回值
     *  3.方法名称自定义
     *  4.方法没有参数，如果有是JoinPoint
   
    * @After :最终通知
     *    属性： value 切入点表达式
     *    位置： 在方法的上面
     * 特点：
     *  1.总是会执行
     *  2.在目标方法之后执行的
     *
     *  try{
     *      SomeServiceImpl.doThird(..)
     *  }catch(Exception e){
     *
     *  }finally{
     *      myAfter()
     *  }
     *
     */
    @After(value = "execution(* *..SomeServiceImpl.doThird(..))")
    public  void  myAfter(){
        System.out.println("执行最终通知，总是会被执行的代码");
        //一般做资源清除工作的。
     }
```

## @PointCut非通知注解

一般用于辅助

```java
* @Pointcut: 定义和管理切入点， 如果你的项目中有多个切入点表达式是重复的，可以复用的。
     *            可以使用@Pointcut
     *    属性：value 切入点表达式
     *    位置：在自定义的方法上面
     * 特点：
     *   当使用@Pointcut定义在一个方法的上面 ，此时这个方法的名称就是切入点表达式的别名。
     *   其它的通知中，value属性就可以使用这个方法名称，代替切入点表达式了
     */
    @Pointcut(value = "execution(* *..SomeServiceImpl.doThird(..))" )
    private void mypt(){
        //无需代码，
    }

 		@After(value = "mypt()")
    public  void  myAfter(){
      ....
    }
```

# Spring与Mybatis的集成

使用IOC来实现(因为IOC能够创建并管理对象),将实现Mybatis所需要的对象交由Spring来管理

> 因为mybatis的自带连接池面对大型项目较为乏力,所以我们使用独立的连接池,这个连接池对象也交由spring来管理

我们需要让spring来创建并管理以下(额外实现mybatis所需要)的对象

1.独立的连接池类的对象， 使用阿里的druid连接池

2.SqlSessionFactory对象

3.创建出dao对象

## 实现步骤

```
  新建maven项目
1.加入maven的依赖
  1）spring依赖
  2）mybatis依赖
  3）mysql驱动
  4）spring的事务的依赖
  5）mybatis和spring集成的依赖： mybatis官方体用的，用来在spring项目中创建mybatis的SqlSesissonFactory，dao对象的
2.创建实体类
创建dao接口和mapper文件
创建mybatis主配置文件
创建Service接口和实现类,声明dao作为属性(方便使用set注入动态创建的daoImpl对象)。
3.创建spring的配置文件：声明mybatis的对象交给spring创建
 1）数据源DataSource
 2）SqlSessionFactory
 3) Dao对象
 4）声明自定义的service
```

### 1.加入maven依赖

```xml
1.加入maven依赖,所需要的依赖有:

0.单元测试依赖

1.spring核心ioc依赖
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.5.RELEASE</version>
    </dependency>
2.spring事务所需依赖
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>5.2.5.RELEASE</version>
</dependency>
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.5.RELEASE</version>
</dependency>
3.mybatis依赖
<dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.1</version>
</dependency>
4.mybatis和spring集成的依赖      //用来在spring项目中创建mybatis的SqlSesissonFactory，dao对象的
<dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.1</version>
    </dependency>
5.mysql驱动
<dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.9</version>
    </dependency>
6.阿里公司的数据库连接池
<dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.12</version>
    </dependency>
```

### 2.创建spring/mybatis所需要的类和配置文件

```java
2.创建spring/mybatis所需要的类  创建实体类/Dao类/Service类/mapper映射文件/mybatis主配置文件/spring配置文件.......
//因为已经不再使用mybatis的连接池,所以主配置文件可以去掉<environments>标签及其内容,创建一个新的mybatis主配置xml文件模板
```

### 3.将datasource对象(druid)交给spring管理

```xml
3.将datasource对象(druid)交给spring管理
druid官网：https://github.com/alibaba/druid

//对象由spring采用bean创建并管理,同时也可以赋值
<bean id="dataSource自定义对象名" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close"> 
     <property name="url" value="${jdbc_url}" />
     <property name="username" value="${jdbc_user}" />
     <property name="password" value="${jdbc_password}" /> 

     <property name="filters" value="stat" />

     <property name="maxActive" value="20" />						//最大容纳的连接对象
     <property name="initialSize" value="1" />		//连接池创建的初始连接
     <property name="maxWait" value="60000" />			//等待时间 60s后如果没有连接成功就会抛出异常
     <property name="minIdle" value="1" />

     <property name="timeBetweenEvictionRunsMillis" value="60000" />
     <property name="minEvictableIdleTimeMillis" value="300000" />

     <property name="testWhileIdle" value="true" />
     <property name="testOnBorrow" value="false" />
     <property name="testOnReturn" value="false" />

     <property name="poolPreparedStatements" value="true" />
     <property name="maxOpenPreparedStatements" value="20" />

     <property name="asyncInit" value="true" />
 </bean>
  
//至少需要配置url/username/password/maxActive 四项
//Druid会自动跟url识别驱动类名，不用配置driverClassName/如果连接的数据库非常见数据库，需要配置属性driverClassName
```

### 4.将sqlSessionFactory对象交给spring管理

```xml
<bean id="sqlSessionFactory自定义对象名称" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--set注入，把数据库连接池付给了dataSource属性-->
        <property name="dataSource" ref="对象id" />
        <!--mybatis主配置文件的位置,configLocation(由spring提供的类)属性是Resource类型，用来读取配置文件
           它的赋值，使用value，在spring文件中指定文件的路径，使用classpath:表示文件的位置-->
        <property name="configLocation" value="classpath:mybatis.xml(文件名,因为是resource包下的,文件在根路径)" />
    </bean>
//SqlSessionFactoryBean内部创建SqlSessionFactory的对象
//configLocation比较特殊,使用value赋值
```

### 5.将dao对象交给spring管理

通过sqlSession.getMapper()获取的动态生成的DaoImpl.我们通过Spring来代理创建并管理

==注意:8大基本类型及String类型采用value赋值,其他引用类型采用ref赋值==

==其他的,CRUD的意思是增删改查==

==当spring和mybatis集成时,事务是自动提交的==(与MapperScannerConfigurer底层创建SqlSession有关????)

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--指定SqlSessionFactory对象的id-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory自定义id" />
        <!--指定包名， 包名是dao接口所在的包名。
            MapperScannerConfigurer会扫描这个包中的所有接口，把每个接口都执行
            一次getMapper()方法，得到每个接口的dao对象。
            创建好的dao对象放入到spring的容器中的。 dao对象的默认名称是 接口名首字母小写
        -->
        <property name="basePackage" value="com.bjpowernode.dao(包名)"/>
    </bean>
//MapperScannerConfigurer在内部调用getMapper()生成每个dao接口的代理对象。(自动创建sqlSession?)
```

### 6.将service等其他对象交由spring管理

即使不集成也需要做的一步…

```xml
<bean id="studentService" class="com.bjpowernode.service.impl.StudentServiceImpl">
  <!--因为service的使用需要调用dao对象,所以设置dao属性,将spring创建管理的daoImpl对象set注入到service中-->
        <property name="studentDao" ref="studentDao(spring自动创建的daoImpl的名称,默认为Dao接口名字的首字母小写)" />
    </bean>
```

## 优化

### 将数据库连接信息写入属性文件

为了便于维护，可以将数据库连接信息写入到属性文件中，使 Spring 配置文件从中读取数据。

> 就是之前mybatis时配置的properties

```xml
1.在resources包中创建properties文件,写入数据库信息

jdbc.url=jdbc:mysql://localhost:3306/springdb
jdbc.username=root
jdbc.passwd=123456
jdbc.max=30

2.获取properties文件位置
<context:property-placeholder location="classpath:xxxx.properties" />

3.注入数据
<property name="url" value="${jdbc.url}" /><!--setUrl()-->
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.passwd}" />
<property name="maxActive" value="${jdbc.max}" />

//注意,因为是value注入的,所以属性文件properties的属性名称可以任意取,但是一般要按照规范
```

# Spring事务

事务原本是数据库中的概念，在 Dao 层。

但一般情况下，需要将事务提升到业务层， 即 Service 层。

这样做是为了能够使用事务的特性来管理具体的业务。 

在 Spring 中通常可以通过以下两种方式来实现对事务的管理：

（1）使用 Spring 的事务注解管理事务 

（2）使用 AspectJ 的 AOP 配置管理事务

## @Transactional注解管理事务 

spring框架自己用aop实现给业务方法增加事务的功能， 使用@Transactional注解增加事务。

适合中小项目使用的， 注解方案。

放在public方法的上面，表示当前方法具有事务。
  可以给注解的属性赋值，表示具体的隔离级别，传播行为，异常信息等等

```java
Transactional属性:
propagation：用于设置事务传播属性。该属性类型为 Propagation 枚举，默认值为Propagation.REQUIRED
isolation：用于设置事务的隔离级别。该属性类型为 Isolation 枚举，默认值为Isolation.DEFAULT
readOnly：用于设置该方法对数据库的操作是否是只读的。该属性为 boolean，默认值为 false。//用于只查询的数据库,可以提高访问速度
timeout：用于设置本操作与数据库连接的超时时限。单位为秒，类型为 int，默认值为-1，即没有时限。//一般不设置

遇到该异常回滚属性:
rollbackFor
rollbackForClassName
遇到该异常不回滚属性
noRollbackFor
noRollbackForClassName

注意:
//@Transactional 若用在方法上，只能用于 public 方法上。
//对于其他非 public方法，如果加上了注解@Transactional，虽然 Spring 不会报错，但不会将指定事务织入到该方法中。
//因为 Spring 会忽略掉所有非 public 方法上的@Transaction 注解。
//若@Transaction 注解在类上，则表示该类上所有的方法均将在执行时织入事务。
```

### 使用步骤:

```xml
 <!--1. 声明事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<!--连接的数据库， 指定数据源-->
<property name="dataSource" ref="数据源对象名称" />
</bean>

<!--2. 开启事务注解驱动，告诉spring使用注解管理事务，使用aop机制，创建@Transactional所在的类代理对象，给方法加入事务的功能
           transaction-manager:事务管理器对象的id-->
//annotation-driven选择时注意事务处理的结尾都要带tx
<tx:annotation-driven transaction-manager="transactionManager" />

3.在service需要使用事务的方法上加上@Transactional注解
<!--默认的传播行为是REQUIRED，默认的隔离级别DEFAULT,默认抛出运行时异常，回滚事务。
    大部分情况可以直接使用@Transactional不加任何值,这等于
@Transactional(
            propagation = Propagation.REQUIRED,
            isolation = Isolation.DEFAULT,
            readOnly = false,
            rollbackFor = {
                   Runn....exp
            }
-->
		<* rollbackFor:表示发生指定的异常一定回滚.
     *   处理逻辑是：
     *     1) spring框架会首先检查方法抛出的异常是不是在rollbackFor的属性值中
     *         如果异常在rollbackFor列表中，不管是什么类型的异常，一定回滚。
     *     2) 如果你的抛出的异常不在rollbackFor列表中，spring会判断异常是不是RuntimeException,
     *         如果是一定回滚。>
4.调用getbean()创建该service对象时就会自动创建代理对象

<!--大致原理:动态创建了带有切片功能的代理对象
 
在你的业务方法执行之前，先开启事务，在业务方法之后提交或回滚事务，使用aop的环绕通知
       @Around("你要增加的事务功能的业务方法名称")
		 Object myAround(){
           spring开启事务
			  try{
			     		事务
				  spring的事务管理器.commit();
			  }catch(Exception e){
          spring的事务管理器.rollback();
			  }
 }
-->
```

## 使用 AspectJ 的 AOP 配置管理事务

适合大型项目，有很多的类，方法，需要大量的配置事务，使用aspectj框架功能，在spring配置文件中
  声明类，方法需要的事务。

声明式事务处理：和源代码完全分离的

这种方式业务方法和事务配置完全分离。

### 使用步骤:

```xml
1.加入aspectj所需要的依赖(一般集成的时候就已经加入了)
<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aspects</artifactId>
		<version>5.2.5.RELEASE</version>
</dependency>

2.声明事务管理器对象
<bean id="transactionManager自定义名称" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<property name="dataSource" ref="数据源对象名称" />
</bean>

3.声明业务方法的事务属性（隔离级别，传播行为，超时时间）

 <tx:advice id="配置内容的名称" transaction-manager="transactionManager事务管理器id">
        <!--tx:attributes：配置事务属性-->
        <tx:attributes>
            <!--tx:method：给具体的方法配置事务属性，method可以有多个，分别给不同的方法设置事务属性
                name:方法名称，1）完整的方法名称，不带有包和类。
                              2）方法可以使用通配符,* 表示任意字符
                propagation：传播行为，枚举值
                isolation：隔离级别
                rollback-for：你指定的异常类名，全限定类名。 发生异常一定回滚
            -->
            <tx:method name="buy" propagation="REQUIRED" isolation="DEFAULT"
                       rollback-for="java.lang.NullPointerException,com.bjpowernode.excep.NotEnoughException"/>

            <!--使用通配符，指定很多的方法-->
            <tx:method name="add*" propagation="REQUIRES_NEW" />
            <!--指定修改方法-->
            <tx:method name="modify*" />
            <!--删除方法-->
            <tx:method name="remove*" />
            <!--查询方法，query，search，find-->
            <tx:method name="*" propagation="SUPPORTS" read-only="true" />
        </tx:attributes>
    </tx:advice>

4.配置aop
    <aop:config>
        <!--1.配置切入点表达式：指定哪些包中类，要使用事务
            id:切入点表达式的名称，唯一值
            expression：切入点表达式，指定哪些类要使用事务，aspectj会创建代理对象

            com.bjpowernode.service
            com.crm.service
            com.service
        -->
        <aop:pointcut id="servicePt" expression="execution(* *..service..*.*(..))"/>

        <!--2.配置增强器：关联adivce和pointcut
           advice-ref:通知，上面tx:advice哪里的配置
           pointcut-ref：切入点表达式的id
        -->
        <aop:advisor advice-ref="myAdvice" pointcut-ref="servicePt" />
    </aop:config>
```



## 关于事务与Spring事务

1.什么是事务

 通俗来讲,事务是指一组sql语句的集合， 集合中有多条sql语句
  可能是insert ， update ，select ，delete,这些语句同时成功或失败,执行是一致的,作为一个一个整体执行。

> ### 概念
>
> 例如：在[关系数据库](https://baike.baidu.com/item/关系数据库)中，一个事务可以是一条SQL语句，一组SQL语句或整个程序。
>
> ### 特性
>
> 事务是恢复和[并发控制](https://baike.baidu.com/item/并发控制)的基本单位。
>
> 事务应该具有4个属性：原子性、一致性、隔离性、持久性。这四个属性通常称为**ACID特性**。
>
> 原子性（atomicity）。一个事务是一个不可分割的工作单位，事务中包括的操作要么都做，要么都不做。
>
> 一致性（consistency）。事务必须是使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的。
>
> 隔离性（isolation）。一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。
>
> 持久性（durability）。持久性也称永久性（permanence），指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。



2.在什么时候想到使用事务

需要事务中的多个sql语句同时成功才能实现功能,或者都失败,保证操作是符合要求的。



3.通常使用JDBC访问数据库， 还有mybatis访问数据库怎么处理事务

 jdbc访问数据库，处理事务  Connection conn ; conn.commit(); conn.rollback();
	

mybatis访问数据库，处理事务， SqlSession.commit();  SqlSession.rollback();
	

hibernate访问数据库，处理事务， Session.commit(); Session.rollback();



4.3问题中事务的处理方式，有什么不足

1)不同的数据库访问技术，处理事务的对象，方法不同，需要了解不同数据库访问技术使用事务的原理

2)掌握多种数据库中事务的处理逻辑。什么时候提交事务，什么时候回顾事务

3)处理事务的多种方法。

总结： 就是多种数据库的访问技术，有不同的事务处理的机制，对象，方法。



5.怎么解决不足

spring提供一种处理事务的统一模型， 能使用统一步骤，方式完成多种不同数据库访问技术的事务处理。

使用spring的事务处理机制，可以完成mybatis访问数据库的事务处理,hibernate访问数据库的事务处理….其他技术访问数据库的事务处理



6.处理事务，需要怎么做，做什么

spring处理事务的模型，使用的步骤都是固定的。把事务使用的信息提供给spring就可以了

  1）事务内部提交，回滚事务，使用的事务管理器对象，代替你完成commit，rollback
     事务管理器是一个接口和他的众多实现类。
	  

接口：PlatformTransactionManager ，定义了事务重要方法 commit ，rollback
	 

实现类：spring把每一种数据库访问技术对应的事务处理类都创建好了。
	           

mybatis访问数据库---spring创建好的是DataSourceTransactionManager
				 

hibernate访问数据库----spring创建的是HibernateTransactionManager

 怎么使用：你需要告诉spring 你用是那种数据库的访问技术，怎么告诉spring呢？
  声明数据库访问技术对于的事务管理器实现类， 在spring的配置文件中使用<bean>声明就可以了

例如，你要使用mybatis访问数据库，你应该在xml配置文件中
  <bean id="xxx" class="...DataSourceTransactionManager"> 

 2）你的业务方法需要什么样的事务，说明需要事务的类型。
     

说明方法需要的事务：
	   

 ①事务的隔离级别isolation：

有4个值。
		

 DEFAULT：采用 DB 默认的事务隔离级别。MySql 的默认为 REPEATABLE_READ； Oracle默认为 READ_COMMITTED。
		

 READ_UNCOMMITTED：读未提交。未解决任何并发问题。
		

 READ_COMMITTED：读已提交。解决脏读，存在不可重复读与幻读。
		

 REPEATABLE_READ：可重复读。解决脏读、不可重复读，存在幻读
		

 SERIALIZABLE：串行化。不存在并发问题。

② 事务的超时时间timeout： 

表示一个方法最长的执行时间，如果方法执行时超过了时间，事务就回滚。
	  单位是秒， 整数值， 默认是 -1. (一般不配置)

③事务的传播行为propagation ： 

控制业务方法是不是有事务的， 是什么样的事务的。
	    

7个传播行为，表示你的业务方法调用时，事务在方法之间是如果使用的。

PROPAGATION_REQUIRED
		

PROPAGATION_REQUIRES_NEW
		

PROPAGATION_SUPPORTS
		

以上三个需要掌握的

PROPAGATION_MANDATORY
		

PROPAGATION_NESTED
		

PROPAGATION_NEVER
		

PROPAGATION_NOT_SUPPORTED

  3）事务提交事务，回滚事务的时机
     

​		①当你的业务方法，执行成功，没有异常抛出，当方法执行完毕，spring在方法执行后提交事务。事务管理器commit
​	 

  	  ②当你的业务方法抛出运行时异常或ERROR， spring执行回滚，调用事务管理器的rollback
     运行时异常的定义： RuntimeException  和他的子类都是运行时异常， 例如NullPointException , NumberFormatException

  	  ③当你的业务方法抛出非运行时异常， 主要是受查异常时，提交事务
    受查异常：在你写代码中，必须处理的异常。例如IOException, SQLException

### 总结spring的事务

1.管理事务的是 事务管理器和他的实现类

2.spring的事务是一个统一模型
     

1）指定要使用的事务管理器实现类，使用<bean>
	  

2）指定哪些类，哪些方法需要加入事务的功能
	  

3）指定方法需要的隔离级别，传播行为，超时

  你需要告诉spring，你的项目中类信息，方法的名称，方法的事务传播行为。

# Spring 与 Web

web项目中容器对象只需要创建一次，所以我们把容器对象放入到全局作用域ServletContext中。(因为多个Servlet都需要用到Spring容器)

实现：使用监听器 当全局作用域对象被创建时 创建容器 存入ServletContext

此处监听器的作用是

监听全局作用域对象的创建

1）创建容器对象，执行 ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
	

2）把容器对象放入到ServletContext， ServletContext.setAttribute(key,ctx)

监听器可以自己创建，也可以使用框架中提供好的==ContextLoaderListener==(这个监听器作用就是以上两点,已经被Spring框架封装好了)

## 实现步骤

```xml
1.加入依赖
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-web</artifactId>
<version>5.2.5.RELEASE</version>
</dependency>

2.在web项目的web.xml文件中注册监听器ContextLoaderListener
<listener>
   <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!--该监听器默认读取的spring配置文件的路径和文件名是/WEB-INF/applicationContext.xml,一般修改默认路径-->

3.指定 Spring 配置文件的位置
//在web.xml中
<context-param>
        <!-- contextConfigLocation:表示配置文件的路径  -->
        <param-name>contextConfigLocation</param-name>
        <!--自定义配置文件的路径(因为是resources包中,所以是class编译文件夹下的根路径classpath:xxx.xml)-->
        <param-value>classpath:spring.xml</param-value>
</context-param>

源代码
ContextLoaderListener extends ContextLoader
ContextLoader 存在属性:WebApplicationContext
WebApplicationContext extends ApplicationContext
即
ApplicationContext:javase项目中使用的容器对象
WebApplicationContext：web项目中的使用的容器对象

ContextLoader 存在方法:initWebApplicationContext(ServletContext servletContext)
该方法关键语句:servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, this.context)
即保存
k:WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE
v:Spring容器
到servletContext
```

```java
//使用框架中的方法，获取容器对象
WebApplicationContext ctx = null;
ServletContext sc = getServletContext();
ctx = WebApplicationContextUtils.getRequiredWebApplicationContext(sc);//将servletContext对象作为参数传入框架方法

//底层实现大致为....
 				WebApplicationContext ctx = null;
        String key =  WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE;
        Object attr  = getServletContext().getAttribute(key);
        if( attr != null){
            ctx = (WebApplicationContext)attr;
        }
```

