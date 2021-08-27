# Mybatis 的使用

## Mybatis 的作用

Mybatis 可以让程序员不用在编写各实体类和 DAO 的具体实现。实体类只需要给出相应的字段；DAO 只需要给出对应的接口，而不再需要编写对应 DAO 的实现类。在 Mybatis 中习惯性将 DAO 写成 Mapper

## 快速使用

### 编写配置文件

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
    <!--
        配置使用何种数据库,常见的数据库:mysql, oracle
        default的值是 environment 标签的 id 值
    -->
    <environments default="development">
        <environment id="development">
            <!-- 配置数据库的事务管理器： -->
            <transactionManager type="JDBC"/>

            <!--
                配置dataSource获取数据库连接 Connection 对象
                type属性指定如何获取数据库连接 Connection 对象
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
         * 获取到的SqlSession对象默认是不自动提交事务，因此在执行 insert, update, delete之后需要手动的提交食物
         * 如果需要SqlSession自动提交事务需要给 openSession传递一个 布尔值：true 
         * SqlSession接口中定义了操作数据的方法，其中SqlSession接口的实现类DefaultSqlSession实现了这些方法
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

     
    <select id="getStudentById" parameterType="int" resultType="com.autmaple.mybatis.entity.Student">
        select * from student where id = #{id}
    </select>

	<!-- 插入方法 -->      
    <insert id="insertStudent" parameterType="com.autmaple.mybatis.entity.Student">
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

增删改査只需要进行配置即可，不需要程序员去实现，程序员只需要知道编写 sql 语句就行。在 xml 文件中配置增删改查时，需要配置 sql ， 以及对应 dao 接口中方法即可。xml 中通过 namespace 指定使用哪个 dao 接口；通过使用 id 与对应的方法进行绑定

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

## 结果集映射 resultMap

结果集映射可以让程序员自定义表和 entity 的映射关系。

```XML
<resultMap id="studentMap" type="com.autmaple.mybatis.entity.Student">
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

方法参数传递的属性 ==> resource/url 属性中指定的配置文件 ==> properties 元素中指定的属性

