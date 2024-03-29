# Mybatis 介绍

MyBatis 是一个基于 SqlSessionFactory 构建的框架。对于 SqlSessionFactory 而言，它的作用是生成 SqlSession 接口对象，这个接口对象是 MyBatis 操作的核心，而在 MyBatis-Spring 的结合中甚至可以“擦除”这个对象，使其在代码中“消失”，这样做的意义是重大的，因为 SqlSession 是一个功能性的代码，“擦除”它之后，就剩下了业务代码，这样就可以使得代码更具可读性。因为 SqlSessionFactory 的作用是单一的，只是为了创建核心接口 SqlSession，所以在 MyBatis 应用的生命周期中理当只存在一个 SqlSessionFactory 对象，并且往往会使用单例模式。而构建 SqlSessionFactory 是通过配置类（Configuration）来完成的

![image-20220708122012608](../../Attachment/image-20220708122012608.png)

从图 5-5 可以了解 MyBatis 可配置的内容。

- properties（属性）：属性文件在实际应用中一般采用 Spring 进行配置
- settings（设置）：它的配置将改变 MyBatis 的底层行为，可以配置映射规则，如自动映射和驼峰映射、执行器（Executor）类型、缓存等内容，比较复杂，具体配置项可参考 MyBatis [官方网站](http://www.mybatis.org/mybatis-3/zh/configuration.html#settings)。 

- typeAliases（类型别名）：因为使用类全限定名会比较长，所以 MyBatis 会对常用的类提供默认的别名，此外还允许我们通过 typeAliases 配置自定义的别名。

- typeHandlers（类型处理器）：这是 MyBatis 的重要配置之一，在 MyBatis 写入和读取数据库的过程中对于不同类型的数据（对于 Java 是 JavaType，对于数据库则是 JdbcType）进行自定义转换，在大部分的情况下我们不需要使用自定义的 typeHandler，因为在 MyBatis 自身就已经定义了比较多的 typeHandler，MyBatis 会自动识别 javaType 和 jdbcType，从而实现各种类型的转换。一般来说，typeHandler 的使用集中在枚举类型上。

- objectFactory（对象工厂）：这是一个在 MyBatis 生成返回的 POJO 时会调用的工厂类。一般我们使用 MyBatis 默认提供的对象工厂类(DefaultObjectFactory)就可以了，而不需要任何配置。

- plugins（插件）：有时候也称为拦截器，是 MyBatis 最强大也是最危险的组件，它通过动态代理和责任链模式来完成，可以修改 MyBatis 底层的实现功能。掌握它需要比较多的 MyBatis 知识，可参考相关的书籍和资料。

- environments（数据库环境）：可以配置数据库连接内容和事务。一般而言，这些交由 Spring 托管。

- databaseIdProvider（数据库厂商标识）：允许 MyBatis 配置多类型数据库支持，不常用。

- mappers（映射器）：是 MyBatis 最核心的组件，它提供 SQL 和 POJO 映射关系，这是 MyBatis 开发的核心。

# Mybatis 的使用

## 常用的注解

| 注解            | 描述                                                  |
| --------------- | ----------------------------------------------------- |
| @Alias          | 指定类的别名，别名可在 Mapper 的 sql 语句中进行使用   |
| @MappedJdbcType | 声明某个字段的类型为 JdbcType 主要用在 typeHandler 中 |
| @MappedType     | 申明某个字段为 JavaType 主要用在 typeHandler 中       |
| @Mapper         | 声明一个类是 Repository 层的对象                      |
| @Param          | 指定 Mapper 文件中 sql 语句拼接时的参数名             |

## Mybatis 的作用

Mybatis 可以让程序员不用在编写各实体类和 DAO 的具体实现。实体类只需要给出相应的字段；DAO 只需要给出对应的接口，而不再需要编写对应 DAO 的实现类。在 Mybatis 中习惯性将 DAO 写成 Mapper

## 快速使用

### 编写配置文件

jdbc.properties文件

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/ssm
username=root
password=root
```

resources.xml : 该配置文件用于设置数据库相关的配置以及各表对应的 DAO

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 4.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--
        引入外部的properties中的配置
        resource: properties配置文件的位置
        根据项目编译之后存放的位置来填写
    -->
    <properties resource="conf/jdbc.properties"/>
    
     <!--
		另外一种配置方式
        <properties>
            <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/db1?serverTimezone=Asia/Shanghai"/>
            <property name="username" value="root"/>
            <property name="password" value="root"/>
        </properties>
	 -->
    
    <!--输出日志-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    
    <!--
        配置使用何种数据库,常见的数据库:mysql, oracle
        default的值是 environment 标签的 id 值
    -->
    <environments default="development">
        <environment id="development">
            <!-- 使用jdbc的事务管理器-->
            <transactionManager type="JDBC"/>

            <!--
                配置dataSource获取数据库连接 Connection 对象
                type属性指定如何获取 Connection 对象
                type = POOLED 表示从连接池中获取 Connection 对象
            -->

            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--告诉mybatis执行sql语句位置-->
    <mappers>
        <!--
            mapper用于指定映射文件存放的位置，映射文件中存放的是sql语句
            resource的值为项目编译之后文件所在的位置
         -->
        <mapper resource="StudentDao.xml"/>
    </mappers>
</configuration>
```

StudentMapper.xml : 该配置文件用于编写相关的 sql 语句

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace ==> 绑定一个 DAO/Mapper 接口, 并且使用全限定类名-->
<mapper namespace="com.autmaple.dao.StudentDao">
    <!--编写sql语句-->
    <!--赋值原理是列名和resultType属性指定的类的属性名相同时就进行赋值，否则不进行赋值-->
    <select id="selectStudent" resultType="com.autmaple.entity.Student">
        select id,name,email,age from student
    </select>
</mapper>
```

### 编写Mappler

StudentDao.java

mybatis 中只需要编写对应的 dao 接口就行，而不需要编写对应 dao 的实现类

```JAVA
public interface StudentDao {
    List<Student> selectStudent();
}
```

### 编写工具类

MybatisUtil.java

```JAVA
package com.autmaple.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtil {
    public static SqlSessionFactory sqlSessionFactory;
    static{
        try {
            String configFile = "mybatis-config.xml";
            /**Resources 是Mybatis中的一个类，用于读取配置文件*/
            InputStream is = Resources.getResourceAsStream(configFile);
            /**SqlSessionFactory的创建比较耗时，在整个项目中有一个就可以了*/
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession(){
        /**
         * SqlSession接口中定义了操作数据的方法，其中SqlSession接口的实现类DefaultSqlSession实现了这些方法
         * 获取到的SqlSession对象默认是不自动提交事务，因此在执行 insert, update, delete之后需要手动的提交食物
         * 如果需要SqlSession自动提交事务需要给 openSession传递一个 布尔值：true 
         * */
        SqlSession sqlSession = null;
        if(sqlSessionFactory != null) {
            sqlSession = sqlSessionFactory.openSession();
        }
        return sqlSession;
    }
}

```

### 实体类

Student.java

不需要对应的 getter 和 setter 方法

```JAVA
package com.autmaple.entity;

public class Student {
    private Integer id; // 使用包装类是为了防止查询到的数据无法转换成对应的数据类型时，可以赋值为null,而基本数据类型不可以
    private String name;
    private String email;
    private Integer age;

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", age=" + age +
                '}';
    }
}

```

### 使用

```JAVA
SqlSession sqlSession = MyBatisUtil.getSqlSession();

/** 
 * MyBatis 通过动态代理，自动创建 StudentDao 接口的实现类并返回
 * */
StudentDao student = sqlSession.getMapper(StudentDao.class);

List<Student> list = student.getStudentList();
for(Student stu: list){
	System.out.println(stu);
}
sqlSession.close();
```

### 输出

```PLAINTEXT
Student{id=1, name='AutMaple', email='AutMaple609@qq.com', age=22}
Student{id=2, name='Irvin', email='Irvin333@qq.com', age=27}
....
```

## 数据库的增删改查

StudentDao.java

```JAVA
public interface StudentDao {
    // 查询全部的 Student
    List<Student> getStudentList();

    // 根据 Id 查询 Student
    Student getStudentById(int id);

    // 插入数据
    int insertStudent(Student stu);

    // 删除数据
    int deleteStudent(int id);

    int updateStudent(Student stu);
}
```

StudentMapper.xml

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace ==> 绑定一个 DAO/Mapper 接口-->
<mapper namespace="com.autmaple.mybatis.dao.StudentDao">
    <!-- 查询方法 -->
    <select id="getStudentList" resultType="com.autmaple.mybatis.entity.Student">
        select * from student
    </select>

    <!--
		parameterType 属性表示的是 Dao 接口方法中参数的类型，可以是全限定类名，也可以是 Mybatis 定义的别名
		Mybatis 定义的别名可以参考官网的说明

		注意：parameterType这个属性不是强制的，Mybatis通过反射机制可以发现接口参数的类型，所以可以没有。

		Mybatis中使用 #{} 时，会使用 PreparedStatement 这个对象来进行 sql 语句的拼接，因此不用担心 sql 注入的问题
	-->
    <select id="getStudentById" parameterType="int" resultType="com.autmaple.mybatis.entity.Student">
        <!-- #{id} === Student.id -->
        select * from student where id = #{id}
    </select>

	<!-- 插入方法 -->      
    <insert id="insertStudent" parameterType="com.autmaple.mybatis.entity.Student">
        <!-- #{name} === Student.name 其他的以此类推-->
        insert into student(name,age,score,birthday) values (#{name},#{age},#{score},#{birthday})
    </insert>
    
	<!-- 删除方法 -->
    <delete id="deleteStudent" parameterType="int">
        delete from student where id = #{id}
    </delete>
	 <!-- 更新方法 -->
    <update id="updateStudent" parameterType="com.autmaple.mybatis.entity.Student">
        update student set name = #{name}, age = #{age}, score=#{score},birthday=#{birthday} where id = #{id}
    </update>

</mapper>
```

增删改査只需要进行配置即可，不需要程序员去实现，程序员只需要知道编写 sql 语句就行。在 xml 文件中配置增删改查时，需要配置 sql ， 以及对应 dao 接口中方法即可。**xml 中通过 namespace 指定使用哪个 dao 接口；通过使用 id 与对应的方法进行绑定**

```XML
<mapper namespace="com.autmaple.mybatis.dao.StudentDao">
    <!-- 查询方法 -->
    <select id="...">...</select> 
    
    <!-- 插入方法 -->
    <insert id="...">...</insert>
    
    <!-- 删除方法 -->
    <delete id="...">...</delete>
    
    <!-- 更新方法 -->
    <update id="...">...</update>
    ...
</mapper>
```

### 传递多个参数

传递多个参数可以在 dao 接口的方法参数中加上 `@Param` 注解。

StrudentDao.java

```java
public interface StudentDao{
    
    List<Student> selectByMultiParams(@Param("myname") String name,
                                      @Param("myage") Integer age);
}
```

Mybatis 的 Mapper 配置文件中

```xml 
<select id="selectMultiParams" resultType="...">
	select * from Student where name =#{myname} or age=${myage}
</select>
```

### 传递Map

在传递参数时，可以传递 Map，当表中字段很多时，Map 将会很方便，Map 允许我们自定义传递的数据的名字

```JAVA
SqlSession sqlSession = MyBatisUtil.getSqlSession();
StudentDao student = sqlSession.getMapper(StudentDao.class);
HashMap<String, Object> map = new HashMap<>();
map.put("studentId", 1);
Student stu = student.getStudentById2(map);
System.out.println(stu);
```

```xml
<select id="getStudentById2" parameterType="map" resultType="com.autmaple.mybatis.entity.Student">
    select * from student where id = #{studentId}
</select>
```

### # 和 $

\#：占位符，告诉 mybatis 使用实际的参数值代替。并使用 PrepareStatement 对象执行 sql 语句, #{…} 代替 sql 语句的“?”。这样做更安全，更迅速，通常也是首选做法

`$`：字符串替换，告诉 mybatis 使用 `$` 包含的“字符串”替换所在位置。使用 Statement 把 sql 语句和 `${}` 的内容连接起来 (字符串的拼接)。主要用在替换表名，列名，不同列排序等操作。

## 结果集映射 resultMap

结果集映射可以让程序员自定义表和 entity 的映射关系。自定义列名与类属性的对应关系。

```XML

<resultMap id="studentMap" type="com.autmaple.mybatis.entity.Student">
    <!-- 将数据库中的某一行数据id列的值赋值给Student类中的id属性-->
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <result property="score" column="score"/>
    <result property="birthday" column="birthday"/>
</resultMap>

<select id="getStudentList" resultMap="studentMap">
    select id,name,score,birthday from student
</select>
```

mybatis 会将查询到的结果与 resultMap 中的的 `column`属性进行对比。匹配就进行赋值。

## 配置文件说明

### 动态配置属性

配置文件中的属性可以进行动态配置，而不必写死。mybatis 支持从外部配置文件中读取数据进行设置，但是该配置文件必须是 properties 文件。引用格式

```XML
<properties resource="filepath">
	....
</properties>
```

#### 属性优先级的问题

mysql.properties

```PROPERTIES
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/db1?serverTimezone=Asia/Shanghai
username=root
password=root
```

xml 配置文件中的配置

```XML
<properties resource="mysql.properties">
    <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/db1?serverTimezone=Asia/Shanghai"/>
    <property name="username" value="root"/>
    <property name="password" value="123321"/>
</properties>
```

使用

```xml
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```

在 xml 中既引用了 mysql.properties，又在 xml 中的 properties 标签中编写了对应的 propertiy. 那么 mybatis 会使用哪一个呢？其实 mybatis 对于这种配置，有优先级的问题

Mybatis 中优先级从大到小:

**方法参数传递的属性 ==> resource/url 属性中指定的配置文件 ==> properties 元素中指定的属性**

### 类型别名

resultType 中的属性名太长，可以使用别名来简化书写，先定义后使用

第一种方式：一个一个的定义

```xml
<typeAliases>
    <typeAlias type="com.autmaple.entity.Student" alias="Student"/>
</typeAliases>
```

第二种方式：直接使用类名作为别名

```xml
    <typeAliases>
        <package name="com.autmaple.entity"/>
    </typeAliases>
```

使用这种方式定义的别名就是指定包下面的类名

使用第二种方式时，如果两个不同的包下存在相同的类名就会报错

[注]：**不推荐使用别名**，建议使用全限定类名

## ResultType 与查询数据之间的关系

查询到的结果分为 ：一行一列，一行多列以及多行多列

一行一列的数据使用基本数据类型

```xml
<select id="selectStudent" resultType="int">
    select count(*) from student
</select>
```

一行多列的数据可以使用 Map 数据类型或者类名。使用 Map 时，列名为 Map 的 key, 列值为 Map 的 value。使用类时，根据类的属性名与列名是否相等来赋值

传递类名

```xml
<select id="selectStudent" resultType="com.autmaple.entity.Student">
    select id,name,email,age from student
</select>
```

传递 map

```xml
<select id="selectStudent" resultType="map">
    select id,name,email,age from student
</select>
```

多行多列数据使用的就是 List 数据类型，此时传递给 ResultType 的类型是 List 中要存储的数据类型。

```xml
<select id="selectStudent" resultType="com.autmaple.entity.Student">
    select id,name,email,age from student
</select>
```

## 模糊查询

模糊查询的实现有两种方式， 一是 java 代码中给查询数据加上“%” ; 二是在 mapper 文件 sql 语句的条件位置加上“%”

1. java 中指定(推荐使用)

   ```java
   String name="%力%";
   List<Student> stuList = studentDao.selectLikeFirst(name);
   stuList.forEach( stu -> System.out.println(stu));
   ```

   ```xml
   <select id="selectLikeFirst" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student
    where name like #{studentName}
   </select>
   ```

2. mapper 文件中的 sql 中拼接

   ```java
   String name="力";
   List<Student> stuList = studentDao.selectLikeSecond(name);
   stuList.forEach( stu -> System.out.println(stu));
   ```

   ```xml
   <select id="selectLikeSecond" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student
    where name like "%" #{studentName} "%"
   </select>
   ```

## 动态 sql

动态 SQL，通过 MyBatis 提供的各种标签对条件作出判断以实现动态拼接 SQL 语句。这里的条件判 断使用的表达式为 OGNL 表达式

常用的动态 SQL 标签有`<if/>、<where/>、<choose/>、<foreach/>`等。 MyBatis 的动态 SQL 语句，与 JSTL 中的语句非常相似

动态 SQL，主要用于解决查询条件不确定的情况：在程序运行期间，根据用户提交的查询条件进行查询

提交的查询条件不同，执行的 SQL 语句不同。若将每种可能的情况均逐一列出，对所有条件进行排列组合，将会出现大量的 SQL 语句

此时，可使用动态 SQL 来解决这样的问题

**注意:**

在 mapper 的动态 SQL 中若出现大于号（>）、小于号（<）、大于等于号（>=），小于等于号（<=）等符号，最好将其转换为实体符号

否则，XML 可能会出现解析出错问题。 特别是对于小于号（<），在 XML 中是绝不能出现的。否则解析 mapper 文件会出错

实体符号表：

| 符号 | 实体符号 |
| ---- | -------- |
| `<`  | `&lt;`   |
| `>`  | `&gt;`   |
| `>=` | `&gt;=`  |
| `<=` | `&lt;=`  |

### `<if/>`标签

对于该标签的执行，当 test 的值为 true 时，会将其包含的 SQL 片断拼接到其所在的 SQL 语句中

语法：`<if test="条件">` sql 语句的部分 ` </if>`
```xml
<select id="selectStudentIf" resultType="com.bjpowernode.domain.Student">
 select id,name,email,age from student
 where 1=1
 <!--当Student.name 不为空null或者不为空字符串时，就将if标签中的语句拼接到if标签外的sql语句中-->
 <if test="name != null and name !='' ">
 and name = #{name}
 </if>
 <if test="age > 0 ">
 and age &gt; #{age}
 </if>
</select>
```

### `<where> ` 标签

`<if/>` 标签的中存在一个比较麻烦的地方：需要在 where 后手工添加 1=1 的子句

因为，若 where 后的所有 `<if/>` 条件均为 false，而 where 后若又没有 1=1 子句，则 SQL 中就会只剩下一个空的 where，SQL
出错。所以，在 where 后，需要添加恒为 true 的子句 1=1，以防止这种情况的发生。但当数据量很大时，这个语句会严重影响查询的效率

使用 `<where/>` 标签，在有查询条件时，可以自动添加上 where 子句；没有查询条件时，不会添加 where 子句。需要注意的是，第一个 `<if/>` 标签中的 SQL 片断，可以不包含 and。不过，写上 and 也不错， 系统会将多出的 and 去掉。但其它 `<if/>` 中 SQL 片断的 and，必须要求写上。否则 SQL 语句将拼接出错 

语法： `<where>` 其他动态 sql `<where/>`

```xml
<select id="selectStudentWhere" resultType="com.bjpowernode.domain.Student">
 select id,name,email,age from student
 <where>
     <if test="name != null and name !='' ">
     and name = #{name}
     </if>
     <if test="age > 0 ">
     and age &gt; #{age}
     </if>
 </where>
</select>
```

### `<foreach>` 标签

`<foreach>` 标签用于实现对于数组与集合的遍历，主要用于 sql 语句中的 in 情况。对其使用，需要注意：

- collection 表示要遍历的集合类型, list ，array 等

- open、close、separator 为对遍历内容的 SQL 拼接

语法： 

```xml
<foreach collection="集合类类型" open="开始的字符" close="结束的字符" item="集合的成员" separator="集合成员之间的分割福">
 #{item}的值
</foreach>
```

```xml
<select id="selectStudentForList" resultType="com.bjpowernode.domain.Student">
 select id,name,email,age from student
 <if test="list !=null and list.size > 0 ">
 	 where id in
     <foreach collection="list" open="(" close=")" item="stuid" separator=",">
         #{stuid}
     </foreach>
 </if>
</select>
```

### 代码片段标签 `<sql>`

`<sql>` 标签用于定义 SQL 片断，以便其它 SQL 标签复用。而其它标签使用该 SQL 片断，需要使用 `<include>` 子标签

该 `<sql>` 标签可以定义 SQL 语句中的任何部分，所以 `<include>` 子标签可以放在动态 SQL 的任何位置。

```xml
<!--创建 sql 片段 id:片段的自定义名称-->
<sql id="studentSql">
 select id,name,email,age from student
</sql>
<select id="selectStudentSqlFragment" resultType="com.bjpowernode.domain.Student">
    <!-- 引用 sql 片段 -->
    <include refid="studentSql"/>
    <if test="list !=null and list.size > 0 ">
         where id in
         <foreach collection="list" open="(" close=")" item="stuobject" separator=",">
            #{stuobject.id}
         </foreach>
    </if>
</select>
```

## typeHandler

```java
// 声明 JdbcType 为整型
@MappedJdbcTypes(JdbcType.INTEGER) 
// 声明 JavaType 为 SexEnum
@MappedTypes(value=SexEnum.class) 
public class SexTypeHandler extends BaseTypeHandler<SexEnum> { 
 
    // 通过列名读取性别
    @Override 
    public SexEnum getNullableResult(ResultSet rs, String col) throws SQLException { 
        int sex = rs.getInt(col); 
        if (sex != 1 && sex != 2) { 
        	return null; 
        } 
        return SexEnum.getEnumById(sex); 
    } 
    // 通过下标读取性别
    @Override 
    public SexEnum getNullableResult(ResultSet rs, int idx) throws SQLException { 
        int sex = rs.getInt(idx); 
        if (sex != 1 && sex != 2) { 
        	return null; 
        } 
        return SexEnum.getEnumById(sex); 
    } 


    // 通过存储过程读取性别
    @Override 
    public SexEnum getNullableResult(CallableStatement cs, int idx) throws SQLException { 
        int sex = cs.getInt(idx); 
        if (sex != 1 && sex != 2) { 
        	return null; 
        } 
        return SexEnum.getEnumById(sex); 
    } 
    // 设置非空性别参数
    @Override 
    public void setNonNullParameter(PreparedStatement ps, int idx, SexEnum sex, JdbcType jdbcType) throws SQLException { 
    	ps.setInt(idx, sex.getId()); 
    } 
}
```

在 MyBatis 中对于 typeHandler 的要求是实现 `TypeHandler<T>` 接口，而它自身为了更加方便也通过抽象类 `BaseTypeHandler<T>` 实现了 `TypeHandler<T>` 接口，所以这里直接继承抽象类 `BaseTypeHandler<T>` 就可以了。注解 `@MappedJdbcTypes` 声明 JdbcType 为数据库的整型，@MappedTypes 声明 JavaType 为 SexEnum，这样 MyBatis 即可据此对对应的数据类型进行转换了。

# 参数类型处理器

在 MyBatis 映射中，能够自动将 Jdbc 类型映射为 Java 类型，处理基本数据类型，默认的类型处理就足够了，如果是特殊的数据类型，就需要我们自定义类型处理器。

比如，我有一个用户爱好的字段，这个字段，在对象中，是一个 List 集合，在数据库中，是一个 VARCHAR 字段，这种情况下，就需要我们自定义类型转换器，自定义的类型转换器提供两个功能：

1. 数据存储时，自动将 List 集合，转为字符串（格式自定义）
2. 数据查询时，将查到的字符串再转为 List 集合

```java
@MappedJdbcTypes(JdbcType.VARCHAR)
@MappedTypes(List.class)
public class List2VarcharHandler implements TypeHandler<List<String>> {
    public void setParameter(PreparedStatement ps, int i, List<String> parameter, JdbcType jdbcType) throws SQLException {
        StringBuffer sb = new StringBuffer();
        for (String s : parameter) {
            sb.append(s).append(",");
        }
        ps.setString(i, sb.toString());
    }

    public List<String> getResult(ResultSet rs, String columnName) throws SQLException {
        String favs = rs.getString(columnName);
        if (favs != null) {
            return Arrays.asList(favs.split(","));
        }
        return null;
    }

    public List<String> getResult(ResultSet rs, int columnIndex) throws SQLException {
        String favs = rs.getString(columnIndex);
        if (favs != null) {
            return Arrays.asList(favs.split(","));
        }
        return null;
    }

    public List<String> getResult(CallableStatement cs, int columnIndex) throws SQLException {
        String favs = cs.getString(columnIndex);
        if (favs != null) {
            return Arrays.asList(favs.split(","));
        }
        return null;
    }
}
```

自定义的类型转换器上需要添加两个注解：

-  @MappedJdbcTypes 注解指定要处理的 Jdbc 数据类型
-  @MappedTypes 指定要处理的 Java 类型

这两个注解结合起来，就可以锁定要处理的字段

使用时只需要在对应的 Mapper 文件的 Sql 语句中指定类型处理器即可：

```xml
<insert id="addUser" parameterType="org.javaboy.mybatis.model.User">
    insert into user (favorites) values (#{favorites,typeHandler=org.javaboy.mybatis.typehandler.List2VarcharHandler});
</insert>
```

# 主键回填

## JDBC 实现

JDBC 中实现主键回填其实非常容易，主要是在构造 PreparedStatement 时指定需要主键回填，然后在插入成功后，查询刚刚插入数据的 id:

```java
public int insert(Person person) {
    Connection con = null;
    PreparedStatement ps = null;
    ResultSet rs = null;
    con = DBUtils.getConnection();
    ps = con.prepareStatement("INSERT INTO person(username,password,money) VALUES(?,?,?)", PreparedStatement.RETURN_GENERATED_KEYS);
    ps.setObject(1, person.getUsername());
    ps.setObject(2, person.getPassword());
    ps.setObject(3, person.getMoney());
    int i = ps.executeUpdate();
    rs = ps.getGeneratedKeys();
    int id = -1;
    if (rs.next()) {
        id = rs.getInt(1);
    }
    return id;
}
```

和普通的插入 SQL 不同之处主要体现在两个地方：

1. 第一个是构造 PreparedStatement 时，多了一个参数，指定了需要主键回填。
2. 在更新操作执行完成之后，调用 getGeneratedKeys ，然后又会获取到一个 ResultSet 对象，从这个游标集中就可以获取到刚刚插入数据的id。

## Mybatis 中的实现

一般情况下，主键有两种生成方式：

1. 主键自动增长
2. 自定义主键(一般是 UUID 或者是类 UUID)

如果是第二种，主键一般是在 Java 代码中生成，然后传入数据库执行插入操作，如果是第一个主键自增长，此时，Java 可能需要知道数据添加成功后的主键。

在 Mybatis 中实现主键回填的方式一共有两种.

### 方式一

在 mapper 文件中 sql 语句中加上 useGeneratekeys 属性，同时加上接收回传 id 的属性

```xml
<insert id="insertBook" useGeneratedKeys="true" keyProperty="id">
    insert into t_book (b_name,author) values (#{name},#{author});
</insert>
```

在执行上述语句后，传入的对象的 id 会被自动的设置

**推荐使用这种方式，这种方式是最简单的**

### 方式二

使用 Mysql 自带的 `last_insert_id()` 函数查询刚刚插入的 id

```xml
<insert id="insertBook">
    <selectKey keyProperty="id" resultType="java.lang.Integer">
        SELECT LAST_INSERT_ID()
    </selectKey>
    insert into t_book (b_name,author) values (#{name},#{author});
</insert>
```

这种方式是在 insert 节点中添加 selectKey 来实现主键回填，实际上这种方式的功能更加丰富，因为 selectKey 节点中的 SQL 我们既可以在插入之前执行，也可以在插入之后执行（通过设置节点的 Order 属性为 AFTER 或者 BEFORE 可以实现），具体什么时候执行，还是要看具体的需求，如果是做主键回填，我们当然需要在插入 SQL 执行之后执行 selectKey 节点中的 SQL。

# ${} 和 #{}

使用 `#{}` 编写 SQL 的时候，`#{}`对应的变量会自动加上单引号，而 `${}` 对应的变量不会加上单引号

当我们需要拼接的变量上不能带单引号时，就必须使用`${}`,其他情况都尽量使用 `#{}` 的方式，因为 `${}` 会有 sql 注入的问题。

## 常见的使用 ${} 的情况：

1. 当 sql 中表名是从参数中取的情况

2. order by 排序语句中，因为 order by 后边必须跟字段名，这个字段名不能带引号，如果带引号会被识别会字符串，而不是字段。
