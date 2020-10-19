![image-20201014164600111](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014164600111.png)

![image-20201014164605154](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014164605154.png)

![image-20201014192159438](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014192159438.png)

![image-20201014192616942](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014192616942.png)

![image-20201014193657195](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014193657195.png)

![image-20201014194608351](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014194608351.png)

![image-20201014195231168](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014195231168.png)

![image-20201014195519560](C:\Users\JAVASM\Desktop\UsersJAVASMDesktop新建文件夹\image-20201014195519560.png)

# 是什么,有什么用:

mybatis是一个sql映射框架，提供的数据库的操作能力,是增强的JDBC.

使用mybatis让开发人员集中精神写sql就可以了，不必关心Connection,Statement,ResultSet
  的创建，销毁，sql的执行。 

> iBATIS 一词来源于“internet”和“abatis”的组合，是一个基于 Java 的持久层框架。
>
> iBATIS 提供的 持久层框架包括 SQL Maps 和 Data Access Objects（DAOs）
>
> 一个框架，早期叫做ibatis,  代码在github。
>
>  mybatis是 MyBatis SQL Mapper Framework for Java （sql映射框架）
>
> 1）sql mapper :sql映射
>          可以把数据库表中的一行数据  映射为 一个java对象。
> 	 一行数据可以看做是一个java对象。操作这个对象，就相当于操作表中的数据
>
>  2） Data Access Objects（DAOs） : 数据访问 ， 对数据库执行增删改查。

# 使用步骤:

## 1.加入依赖 

mysql驱动依赖 

mybatis依赖

```xml
<dependency>
 <groupId>org.mybatis</groupId>
 <artifactId>mybatis</artifactId>
 <version>3.5.1</version>
</dependency>

<dependency>
 <groupId>mysql</groupId>
 <artifactId>mysql-connector-java</artifactId>
 <version>5.1.9</version>
 </dependency>
 </dependencies>
```

## 2.在Dao包中创建xml文件

sql映射文件（sql mapper）： 写sql语句的， mybatis会执行这些sql

```java
1.指定约束文件  //创建XML文件后,到官网或其他地方去拷贝格式
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="">
  <select id="" resultType="">
  //sql语句
  </select>
</mapper>
// mybatis-3-mapper.dtd是约束文件的名称， 扩展名是dtd的。  
//约束文件作用： 限制，检查在当前文件中出现的标签，属性必须符合mybatis的要求。

2.mapper 是当前文件的根标签，必须的。
    //namespace：叫做命名空间，唯一值的， 可以是自定义的字符串。
               //要求你使用dao接口的全限定名称。

3.在当前文件中，可以使用特定的标签，表示数据库的特定操作。
   <select>:表示执行查询，select语句
   <update>:表示更新数据库的操作， 就是在<update>标签中 写的是update sql语句
   <insert>:表示插入， 放的是insert语句
   <delete>:表示删除， 执行的delete语句
      //id: 你要执行的sql语法的唯一标识， mybatis会使用这个id的值来找到要执行的sql语句
           //可以自定义，但是要求你使用接口中的方法名称。

       //resultType:表示结果类型的， 是sql语句执行后得到ResultSet,遍历这个ResultSet得到java对象的类型。
          //值写的类型的全限定名称
     
     //注意,namespace和id都可以自定义,但是为了反射需求,都需要按照规范书写
```

##  3.创建 MyBatis 主配置文件

放置于resources包中

```xml
mybatis的主配置文件： 主要定义了数据库的配置信息， sql映射文件的位置  //创建XML文件后,到官网或其他地方去拷贝格式
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
  				//约束文件  mybatis-3-config.dtd
<configuration>
					//configuration根标签
  <environments default="mysql">
  				//环境配置： 数据库的连接信息    default:必须和某个environment的id值一样。告诉mybatis使用哪个数据库的连接信息。也就是访问哪个数据库
    <environment id="mysql">
  			//environment : 一个数据库信息的配置环境    id:一个唯一值，自定义，表示环境的名称。
      <transactionManager type="JDBC"/>
      		//transactionManager事务类型
   				//type: 事务的处理的类型
                     //1）JDBC : 表示mybatis底层是调用JDBC中的Connection对象的，commit， rollback
                     //2）MANAGED : 把mybatis的事务处理委托给其它的容器（一个服务器软件，一个框架（spring））
      <dataSource type="POOLED">
//dataSource数据源   java体系中，规定实现了javax.sql.DataSource接口的都是数据源。数据源表示Connection对象
//type：表示数据源的类型， POOLED表示使用连接池
//1）POOLED: 使用连接池， mybatis会创建PooledDataSource类
//2）UPOOLED: 不使用连接池， 在每次执行sql语句，先创建连接，执行sql，在关闭连接mybatis会创建一个UnPooledDataSource，管理Connection对象的使用
//3）JNDI：java命名和目录服务（windows注册表）
//driver, user, username, password 是固定的，不能自定义。
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/   "/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
  //sql mapper(sql映射文件)的位置     从类路径开始的路径信息。target/clasess(类路径)
    <mapper resource=""/>
  </mappers>
</configuration>


```

## 4.通过mybatis访问数据库

```java
//访问mybatis读取student数据
//1.定义mybatis主配置文件的名称, 从类路径的根开始（target/clasess）
String config="mybatis.xml";
//2.读取主配置文件
InputStream in = Resources.getResourceAsStream(config);
//3.获取SqlSession对象
SqlSessionFactoryBuilder builder  = new SqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(in);
SqlSession sqlSession = factory.openSession();
//4.指定要执行的sql语句的标识 sql映射文件中--->    namespace.id
String sqlId = "com.bjpowernode.dao.StudentDao.insertStudent";
//5.执行sql语句，通过sqlId找到语句
int nums = sqlSession.insert(sqlId,student);
//6.mybatis默认不是自动提交事务的， 所以在insert ，update ，delete后要手工提交事务  select不需要事务
sqlSession.commit();
//7.关闭SqlSession对象
sqlSession.close();

//Resources  mybatis中的一个类， 负责读取主配置文件                其有很多方法通过加载并解析资源文件，返回不同类型的 IO 流对象。
//SqlSessionFactoryBuilder.build()创建SqlSessionFactory       对象在创建完工厂对象后，就完成了其历史使命，即可被销毁。所以，一般会将该 SqlSessionFactoryBuilder 对象创建为一个方法内的局部对象，方法结束，对象销毁。
//SqlSessionFactory 接口对象是一个重量级对象（系统开销大的对象），是线程安全的，所以一个应用只需要一个该对象即可。实现类为DefaultSqlSessionFactory
//openSession(true)：创建一个有自动提交功能的 SqlSession
//openSession(false)：创建一个非自动提交功能的 SqlSession，需手动提交
//openSession()：同 openSession(false)
SqlSession 接口对象用于执行持久化操作。一个 SqlSession 对应着一次数据库会话    //实现类为DefaultSqlSession。
SqlSession对象不是线程安全的，需要在方法内部使用， 在执行sql语句之前，使用openSession()获取SqlSession对象。
SqlSession在执行完sql语句后，需要关闭它，执行SqlSession.close(). 这样能保证他的使用是线程安全的。
//定义了操作数据的方法 例如 selectOne() ,selectList() ,insert(),update(), delete(), commit(), rollback()

```

# mybatis的动态代理

底层是JDK的动态代理?

mybatis的动态代理： mybatis根据 dao的方法调用，获取执行sql语句的信息。

mybatis根据你的dao接口，创建出一个dao接口的实现类， 并创建这个类的对象。

完成SqlSession调用方法， 访问数据库。

```java
为什么使用动态代理以及大致原理

读取主配置文件-->mybatis读取信息链接数据库/读取sql映射文件信息-->sqlSession执行sql语句,使用namespace.id定位所需要执行的sql语句
通过namespace定位xml文件,id定位sql语句
//这个过程并没有使用到Dao层,但是实际项目中需要通过调用Dao层的方法来访问数据库,所以还是需要Dao来制定规则并提供访问的途径service->dao
//而Dao的实现类只是提供了sqlid,和sqlSession方法的调用并没有什么实际的用处,所以可以通过动态创建实现类来减少代码.
public List<Student> selectStudents() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        String sqlId="com.bjpowernode.dao.StudentDao.selectStudents";
        List<Student> students  = sqlSession.selectList(sqlId);
        sqlSession.close();
        return students;
    }
//Dao类全限定名称-->namespace
//Dao调用的方法名-->id
//namespace.id坐标处的标签/方法的返回值-->确定MyBatis要调用的SqlSession的方法
如果返回值是List ，调用的是SqlSession.selectList()方法。
如果返回值 int ，或是非List的， 看mapper文件中的 标签是<insert>，<update> 就会调用
SqlSession的insert， update等方法
```

```java
实现步骤:
SqlSession session = factory.openSession();
StudentDao dao = session.getMapper(StudentDao.class);
Object obj=dao.方法调用();
//提交事务与否
关闭SqlSession
```

# 传入参数

从java代码中把数据传入到mapper文件(sql映射文件)的sql语句中

```java
parameterType ： 写在mapper文件中的 一个属性。 表示dao接口中方法的参数的数据类型。
//注意,不是resultType
//parameterType一般不写,不是强制的.mybatis通过反射机制能够确定接口方法的参数类型,所以可以没有。

  //简单类型： mybatis把java的基本数据类型和String都叫简单类型。
一个简单参数:在mapper文件获取简单类型的一个参数的值，使用 #{任意字符}
一个引用参数(多个参数)：使用java对象作为接口中方法的参数
  //普通的方式  #{ property(属性名),javaType=(java中属性数据类型(全限定名称)),jdbcType=(数据库对应的数据类型) }
  //简化方式	#{ property(属性名) }  mybatis通过反射可以获取到属性数据类型
  //注意,只要拥有set方法的属性的类就可以使用该方法

多个简单参数： 
1.命名参数，在形参定义的前面加入 @Param("自定义参数名称")  #{使用相同定义名称}
2.按位置传值，mybatis。3.4之后 ，使用 #{arg0} ,#{arg1}   , mybatis.3.4之前，使用 #{0} ，#{1}
3.使用Map存放多个值   map<String,Object>   #{key}
```

# 两个占位符\#和$的区别

```java
#{}  1.告诉mybatis使用实际的参数值代替。2.并使用PrepareStatement对象执行.3.执行效率更高,并且可以防止sql注入
${}  1.告诉mybatis使用$包含的“字符串”替换所在位置。2.使用Statement把sql语句和${}的内容连接起来。3.效率低,会出现sql注入
//${}:能使用#{}的地方都可以使用${},但是一般不使用,主要用在替换表名，列名，不同列排序等操作。
//因为${}是字符串拼接,所以传入的非数字的值需要加单引号''
```

# 返回结果

==调用类的无参数构造方法，创建对象==

```xml
resultType属性:  执行 sql 得到 ResultSet 转换的类型，使用类型的完全限定名或别名。 类型是任意的。(不一定是实体类) //简单数据类型是默认别名

返回类型为map:sql 的查询结果作为 Map 的 key 和 value。推荐使用 Map<Object,Object>。
//注意：Map 作为接口返回值，sql 语句的查询结果最多只能有一条记录。大于一条记录是错误。或者使用List<Map>

resultMap标签/属性:结果映射，指定列名和java对象的属性对应关系。
//注意,resultMap跟Map没有关系....
//常用在列名和 java 对象属性名不一样的情况。
//使用:先定义resultMap标签,在DCL标签中使用resultMap引用.定义的resultMap标签可以多次引用
//主键使用id标签,非主键列使用result标签
//column:列名    property:java类型的属性名
<resultMap id="X" type="com.bjpowernode.domain.Student">
		<id column="" property="" />  
		<result column="" property=""/> 
</resultMap>
<select id="" resultMap="X">
		sql
</select>

注意:
//查询语句必须有resultType
//如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身。
//resultType 和 resultMap，不能同时使用。
//当实体类属性名和列名不同时,有两种处理方法 1.给列名起别名 2.使用resultMap
处理方式：  
<--1. mybatis执行sql语句， 然后mybatis调用类的无参数构造方法，创建对象。-->
2. mybatis把ResultSet指定列值付给同名的属性。
```

# 模糊查询Like

```java
模糊查询的实现有两种方式:
1.java 代码中给查询数据加上“%”
sql:where name like #{studentName}
传入实参:%力%
2.在 mapper 文件 sql 语句的条件位置加上“%”
sql:where name like "%" #{studentName} "%"
```

# 动态SQL

动态sql: 根据条件获取到不同的sql语句。主要是where部分发生变化。

动态sql的实现，使用的是mybatis提供的标签， 常用<if> ,<where>,<foreach>

```xml
<select id="selectStudentIf" resultType="com.bjpowernode.domain.Student">
 select id,name,email,age from student
 where 1=1
<if test=”条件”> 
  and  name = #{name}
</if>
</select>

<select>
  <where>
    <if test=”条件”>
    </if>
  </where>
</select>

//1.if标签,当test=true时,将语句加入主sql之后

//2.where标签用来包含多个<if>的，当有if成立时，<where>会自动增加一个where关键字，并去掉if中多余的and，or等。

3.<foreach/>标签用于实现对于数组与集合的遍历。对其使用，需要注意：
//collection 表示要遍历的集合类型, list ，array 等。
//open、close、separator 为对遍历内容的 SQL 拼接。
//主要用在sql的in语句中

<foreach collection="集合类型" open="开始的字符" close="结束的字符"
item="集合中的成员" separator="集合成员之间的分隔符">
 #{item 的值}
</foreach>

例子:
<select id="selectStudentForList"resultType="com.bjpowernode.domain.Student">
 select id,name,email,age from student
 <if test="list !=null and list.size > 0 ">
 where id in
 <foreach collection="list" open="(" close=")"item="X" separator=",">
 	#{X}          
 </foreach>
 </if>
</select>
  
//若遍历的是List<引用类型>,则遍历时#{对象.属性} --->通过反射get属性method
//根据like的用法,我们可以设想到open和close可以不用写,自己拼接上去
//separator也可以通过在sql之后加入","最后多出来的一个","可以通过奇技淫巧屏蔽,例如:(此处的id=-1取不到,于是不会影响结果)
where id in (
        <foreach collection="list" item="stu" >
            #{stu.id},
        </foreach>
        -1 )

4.代码片段
定义代码片段 <sql id="X">  sql语句 </sql>
   引用
<select>
  <include refid="X" /> -->代表了被定义的代码片段
</select>
```

# 辅助分页PageHelper组件(非mybatis框架)

PageHelper 支持多种数据库

```xml
实现步骤：

1.加入依赖
<dependency>
 <groupId>com.github.pagehelper</groupId>
 <artifactId>pagehelper</artifactId>
 <version>5.1.10</version>
</dependency>

2.在mybatis主配置文件加入plugin配置(类似于过滤器)
在<environments>之前加入
<plugins>
 <plugin interceptor="com.github.pagehelper.PageInterceptor" />
</plugins>

3.查询语句之前调用 PageHelper.startPage 静态方法。
PageHelper.startPage(pageNum,pageSize);
List<Student> students = dao.selectAll();
// pageNum: 第几页开始
// pageSize: 一页中有多少行数据

mybatis主配置文件顺序...?
<properties resource="" />
<settings></settings>
<typeAliases></typeAliases>
<plugins></plugins>
<environments></environments>
<mappers></mappers>
```



# 优化与问题解决

## 将XML文件写入IDEA文件模板

editor->file and code templates

将创建文件设置为xml后缀,写入模板

```java
//sql映射文件模板

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="">
  <select id="" resultType="">
  </select>
</mapper>

//mybatis主配置文件模板

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="mysql">
    <environment id="mysql">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value=""/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource=""/>
  </mappers>
</configuration>
```

## 将读取步骤封装

```java
public class MyBatisUtils {

    private  static  SqlSessionFactory factory = null; 
    static {
        String config="mybatis.xml"; // 需要和你的项目中的文件名一样
        try {
            InputStream in = Resources.getResourceAsStream(config);
            factory = new SqlSessionFactoryBuilder().build(in);
						//factory是重量级对象,只需要创建一次
      	} catch (IOException e) {
            e.printStackTrace();
        }
    }


    //获取SqlSession的方法
    public static SqlSession getSqlSession() {
        SqlSession sqlSession  = null;
        if( factory != null){
            sqlSession = factory.openSession();// 非自动提交事务
        }
        return sqlSession;
    }
}
```

## 使用数据库属性配置文件

把数据库连接信息(连接四要素数据)放到一个单独的文件中。 和mybatis主配置文件分开。

目的是便于修改，保存，处理多个数据库的信息。

MyBatis 主配置文件需要从这个属性文件中读取这些数据。

```xml
1.在resources包中定义一个属性配置文件，xxxx.properties
	在属性配置文件中定义数据，格式是 key=value  
	key： 一般使用 . 做多级目录的。一般最少需要一级分级,增加可读性
例如:
			jdbc.driver=com.mysql.jdbc.Driver
		  jdbc.url=jdbc:mysql//.....
		  jdbc.username=root
		  jdbc.password=123456
2.在mybatis的主配置文件，使用<property> 指定文件的位置,从类路径根开始找文件
	在需要使用值的地方，使用${key}
<properties resource="xxxx.properties" /> //写在根标签configuration开始处
<property name="driver" value="${jdbc.driver}"/>
<property name="url" value="${jdbc.url}"/>
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.password}"/>
```

## 关于资源文件resources

```xml
main文件中除了class文件不会编译其他文件,若想编译其他文件需要加入resources依赖

<resource>
 <directory>src/main/java</directory><!--所在的目录-->
 	<includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
 		<include>**/*.properties</include>
 	<include>**/*.xml</include>
 </includes>
 <filtering>false</filtering>
</resource>

```

## mappers（映射器）指定多个mapper文件

```xml
<mappers>
        <!--第一种方式：指定多个mapper文件-->
        <!--<mapper resource="com/bjpowernode/dao/StudentDao.xml"/>
        <mapper resource="com/bjpowernode/dao/OrderDao.xml" />-->

        <!--第二种方式： 使用包名
            name: xml文件（mapper文件）所在的包名, 这个包中所有xml文件一次都能加载给mybatis
            使用package的要求：
             1. mapper文件名称需要和接口名称一样， 区分大小写的一样
             2. mapper文件和dao接口需要在同一目录
        -->
        <package name="com.bjpowernode.dao"/>
       <!-- <package name="com.bjpowernode.dao2"/>
        <package name="com.bjpowernode.dao3"/>-->
 </mappers>
```

## 配置日志功能

mybatis.xml 文件加入日志配置，可以在控制台输出执行的 sql 语句和参数  

```xml
<settings>
 <setting name="logImpl" value="STDOUT_LOGGING" />
</settings>
```

## 自定义别名

```xml
自定义别名,在mybatis主配置文件中定义,可以在resultType中使用
第一种方式：
<typeAliases>
<typeAlias type="com.bjpowernode.domain.Student" alias="stu" />
</typeAliases>
可以指定一个类型一个自定义别名
type:自定义类型的全限定名称
alias:别名
第二种方式:
<typeAliases>
<package name="com.bjpowernode.domain"/>
</typeAliases>
name是包名，这个包中的所有类，类名就是别名（类名不区分大小写）
//开发中使用package别名,一般建议不使用别名,使用全限定名称
```

## idea延迟无法创建xml文件问题

1.通过maven的clean后编译

2.build->rebuild porject 强制重新编译项目

3.file->invalidate caches/restart 清除缓存并重启IDEA

4.手动拷贝xml到编译文件中

## 默认支持的别名和主配置文件设置请查看word文档