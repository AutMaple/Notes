# Mybatis 的使用

## Mybatis 的作用

Mybatis 可以让程序员不用在编写各实体类和 DAO 的具体实现。实体类只需要给出相应的字段；DAO 只需要给出对应的接口，而不再需要编写对应 DAO 的实现类。在 Mybatis 中习惯性将 DAO 写成 Mapper

## 快速使用

### 编写配置文件

resources.xml : 该配置文件用于设置数据库相关的配置以及各表对应的 DAO

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties>
        <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/db1?serverTimezone=Asia/Shanghai"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </properties>
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
    <mappers>
        <mapper resource="StudentMapper.xml"/>
    </mappers>
</configuration>
```

StudentMapper.xml : 该配置文件用于编写相关的 sql 语句

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace ==> 绑定一个 DAO/Mapper 接口-->
<mapper namespace="com.autmaple.mybatis.dao.StudentDao">
    <select id="getStudentList" resultType="com.autmaple.mybatis.entity.Student">
        select * from student
    </select>
</mapper>
```

### 编写Mappler

StudentDao.java

```JAVA
public interface StudentDao {
    List<Student> getStudentList();
}
```

### 编写工具类

MybatisUtil.java

```JAVA
public class MyBatisUtil {
    private static SqlSessionFactory sqlSessionFactory;
    static{
        try {
            String resource = "resource.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```

### 实体类

Student.java

```JAVA
public class Student {
    private int id;
    private String name;
    private int age;
    private double score;
    private Date birthday;

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", score=" + score +
                ", birthday=" + birthday +
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
Student{id=1, name='AutMaple', age=22, score=135.0, birthday=Wed Jun 09 00:00:00 CST 1999}
Student{id=2, name='张三', age=32, score=125.0, birthday=Wed Jun 09 00:00:00 CST 1999}
Student{id=3, name='李四', age=29, score=115.0, birthday=Wed Jun 09 00:00:00 CST 1999}
Student{id=4, name='Irvin', age=27, score=95.0, birthday=Wed Jun 09 00:00:00 CST 1999}
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