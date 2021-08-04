# JDBC 的使用

## 使用步骤

```JAVA
// 1. 注册驱动
Class.forName("com.mysql.cj.jdbc.Driver"); // 注册驱动

// 2. 获取连接对象
Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db1", "root", "root");

// 3. 获取执行 sql 的对象
Statement stmt = conn.createStatement(); // statement 对象

String sql = "update student set name = 'autmaple' where id = 1";

// 4. 执行sql
int count = stmt.executeUpdate(sql); // 执行sql

// 5. 关闭资源
stmt.close(); 
conn.close();
```

## 详细解释

### Connection

- Connection : 数据库连接对象
- 功能：
  1. 获取执行 sql 的对象
     - Statement对象 –> createStatement()方法获取
     - PrepareStatement对象 –> prepareStatement(String sql)方法进行获取
  2. 管理事务
     - 开启事务:`setAutoCommit(boolean autoCommit)`设置为 false 即开启事务
     - 提交事务: `commit()`
     - 回滚事务: `rollback()`

### Statement

Statement: 执行 sql 的对象

执行 sql 的方法

| 方法                                 | 作用                            |
| ------------------------------------ | ------------------------------- |
| `boolean execute(String sql)`        | 执行任意的 sql 语句，不推荐使用 |
| `int executeUpdate(String sql)`      | 执行更新操作                    |
| `ResultSet executeQuery(String sql)` | 执行查询操作                    |

- `int executeUpdate(String sql)`

  返回值为影响的行数，返回值 `> 0` ,执行成功，否则执行失败,返回`0`

#### ResultSet

ResultSet: 结果集对象, 封装查询到的结果

常用的方法

| 方法                                       | 作用                       |
| ------------------------------------------ | -------------------------- |
| `boolean next()`                           | 将游标指向下一行，         |
| `<datatype> get<datatype>(String colName)` | 获取`<datatype>`类型的数据 |

- `boolean next()`

  首先将指针指向下一行，然后判断是否有数据，如果有数据则返回 true ， 否则返回 false

- `String getString(String colName)`

  获取列名为`colName`的列的数据，并返回`String` 类型的数据

## sql 注入问题

在拼接 sql 语句时，有一些 sql 的关键字参与字符串的拼接，会造成安全问题

解决这个问题可以使用 `PreparedStatement`对象来解决

预编译 sql 使用 `?` 作为占位符。

- `set<Type>)(int index, <type> x)`

`set<Type>`方法用于对占位符进行填充，`type`表示需要在占位符中填充的数据类型, `index`表示的是在第几个占位符中填充数据， 编号从`1`开始，使用实例：

```JAVA
// 1. 获取通过Connectiion 对象获取 PreparedStatment 对象
Connection conn = JDBCUtils.getConnection()
PreparedStatement preparedStatement = conn.prepareStatement(sql);

// 2. 编写 sql 语句， 在需要替换数据的地方使用 `?`来进行替换
String sql = "update student set name = ? where id = 1";

// 3. 使用PreparedStatement 对象提供的方法填充占位符
preparedStatement.setString(1, "AutMaple");

// 4. 执行 sql 语句
int count = preparedStatement.executeUpdate();
```

以后都是使用`PreparedStatement`对象来对数据库进行操作，好处：

1. 防止 sql 注入问题
2. 执行 sql 语句的效率更高

## 事务管理

- 开启事务:`setAutoCommit(boolean autoCommit)`设置为 false 即开启事务
- 提交事务: `commit()`
- 回滚事务: `rollback()`

## 数据库连接池

概念：其实就是一个容器集合，存放数据库连接的容器。当系统初始化后，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完后，会将连接对象返还给容器

好处：节约资源，用户访问高效。申请数据库连接是向操作系统申请资源，比较耗时。

实现：数据库厂商去实现 `DataSource`接口，一般不需要我们实现

- C3P0：数据库连接池技术
- Druid：数据库连接池技术，由阿里巴巴实现，性能非常好(**推荐**)

方法：

- `getConnection()`获取连接
- `Connection.close()`归还连接

### C3P0连接池的使用

需要使用对应的 jar 包，可以使用 Maven 自动进行下载

使用C3P0连接池必须创建一个名为`c3p0-config.xml`文件，在该配置文件中进行相关的配置文件

```XML
<c3p0-config>
    <!--使用默认的配置读取数据库连接池对象 -->
    <default-config>
        <!--  连接参数 -->
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/db1?serverTimezone=Asia/Shanghai</property>
        <property name="user">root</property>
        <property name="password">root</property>

        <!-- 连接池参数 -->
        <!--初始化申请的连接数量-->
        <property name="initialPoolSize">5</property>
        <!--最大的连接数量-->
        <property name="maxPoolSize">10</property>
        <!--超时时间-->
        <property name="checkoutTimeout">3000</property>
    </default-config>

    <!--    <named-config name="otherc3p0">-->
    <!--        &lt;!&ndash;  连接参数 &ndash;&gt;-->
    <!--        <property name="driverClass">com.mysql.jdbc.Driver</property>-->
    <!--        <property name="jdbcUrl">jdbc:mysql://localhost:3306/hs_test?serverTimezone=Asia/Shanghai</property>-->
    <!--        <property name="user">root</property>-->
    <!--        <property name="password">root</property>-->

    <!--        &lt;!&ndash; 连接池参数 &ndash;&gt;-->
    <!--        <property name="initialPoolSize">5</property>-->
    <!--        <property name="maxPoolSize">8</property>-->
    <!--        <property name="checkoutTimeout">1000</property>-->
    <!--    </named-config>-->
</c3p0-config>
```

在代码中使用

```JAVA
DataSource ds = new ComboPooledDataSource(); // 使用默认的配置，也可以指定特定的配置名
Connection conn = ds.getConnection();
```

### Druid 连接池的使用

1. 导入 jar 包
2. 设置配置文件，该配置文件名称不做任何限定，但是必须是`properties`类型的文件，并且该文件存放的位置也不限定，由开发人员自行指定
3. 加载配置文件
4. 获取数据库连接池对象： 通过工厂来获取：DruidDataSourceFactory
5. 获取连接

#### 配置文件

配置文件文件名任意， 但是文件类型是`properties`

示例

```PROPERTIES
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/db1
username=root
password=root
# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大超时时间
maxWait=3000
```

#### 使用代码

```JAVA
Properties properties = new Properties();
InputStream is = JdbcDemo01.class.getClassLoader().getResourceAsStream("druid.properties");
properties.load(is);
DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
Connection conn = dataSource.getConnection();
```

## JDBC 工具类(JDBCUtils)

### 无数据库连接池版

jdbc.properties文件

```PROPERTIES
url=jdbc:mysql://localhost:3306/db1
username=root
password=root
driver=com.mysql.cj.jdbc.Driver
```

JDBCUtils类的实现

```java
import java.io.FileReader;
import java.io.IOException;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

public class JDBCUtils {
    private static String url;
    private static String username;
    private static String password;

    static{
        try {
            Properties properties = new Properties();
            ClassLoader loader = JDBCUtils.class.getClassLoader();
            URL resource = loader.getResource("jdbc.properties");
            String path = resource.getPath();
            properties.load(new FileReader(path));
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");
            String driver = properties.getProperty("driver");
            Class.forName(driver);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection(){
        Connection conn = null;
        try {
            conn = DriverManager.getConnection(url, username, password);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        return conn;
    }

    public static void close(Statement stmt, Connection conn){
        close(null, stmt, conn);
    }
    
    public static void close(ResultSet rs, Statement stmt, Connection conn){
        if (rs != null){
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(stmt != null){
            try {
                stmt.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(conn != null){
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```

### 有数据库连接池版本

使用 Druid 数据库连接池

代码实现

```java
public class JDBCUtilsStrong {
    private static DataSource ds;

    static {
        try {
            Properties properties = new Properties();
            InputStream is = JDBCUtilsStrong.class.getClassLoader().getResourceAsStream("druid.properties");
            properties.load(is);
            ds = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

    public static void close(Statement stmt, Connection conn){
        close(null, stmt, conn);
    }
    
    public static void close(ResultSet resultSet, Statement stmt, Connection conn){
        if (resultSet != null){
            try {
                resultSet.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(stmt != null){
            try {
                stmt.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(conn != null){
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```

## JDBC Template的使用

查看文章[JDBC_Template的使用](https://blog.autmaple.com/2021/07/21/JDBC-Template的使用/)