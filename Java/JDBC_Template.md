# JDBC Template 的使用

## 常用的方法

1. `public int update(String sql, Object... args)`
2. `public <T> T queryForObject(String sql, Class<T> requiredType)`
3. `public <T> List<T> queryForList(String sql)`
4. `public Map<String, Object> queryForMap(String sql)`
5. `public <T> List<T> query(String sql, RowMapper<T> rowMapper)`

## 各方法的介绍

### 更新操作

```JAVA
// 执行update, insert, delete 等 DML (修改数据的)语言
public int update(String sql, Object... args);
```

**参数说明**

- `String sql`: 执行的 sql 语句，会在内部封装成`PreparedStatement`对象，因此可以 sql 中可设置占位符
- `Object... args`: 给占位符设置值，从左到由，编号由小到大。

**使用示例**

```JAVA
// 插入数据
JdbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "insert into student(name,age,score,birthday) values(?,?,?,?)";
int count = jtl.update(sql,"James", 18, 123, "2002-10-09");
System.out.println(count);
JAVA
// 更新数据
dbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "update student set name = ? where id = 1";
int count = jtl.update(sql,"AutMaple");
System.out.println(count);
JAVA
// 删除数据
JdbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "delete from student where id = ?";
int count = jtl.update(sql,17);
System.out.println(count);
```

### 查询操作

#### queryForObject

用于查询单行单列的数据

```JAVA
// 查询当行当列的数据
public <T> T queryForObject(String sql, Class<T> requiredType)
```

**参数说明**

- `String sql`: 执行的查询 sql 语句
- `Class<T> requiredType`: 期望返回的数据类型的`class`

**使用示例**

```JAVA
JdbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "select name from student where id = 1;";
String name  = jtl.queryForObject(sql, String.class);
System.out.println(name);
```

**注意**：该方法只能查询某一行中的某一列数据，而不能将查询到的一整行的数据

#### queryForMap

用于查询单行数据

```JAVA
// 查询单行的数据
public Map<String, Object> queryForMap(String sql)
```

**使用示例**

```JAVA
JdbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "select * from student where id = 1;";
Map<String, Object> colMap = jtl.queryForMap(sql);
System.out.println(colMap);
```

返回查到的某一行的数据，如果查询到的结果有多行，则会报错

#### queryForList

用于查询多行多列数据

```JAVA
// 将数据封装到 Map 中
public <T> List<T> queryForList(String sql); 

// 将数据封装到对象中
public <T> List<T> query(String sql, RowMapper<T> rowMapper);
```

1. 将数据封装到 Map 中

```JAVA
// 查询多行多列数据 --> 将数据封装到Map<String, Object> 中
public <T> List<T> queryForList(String sql)
```

将查询到的每一行封装成到 一个Map 中，然后将每一个 Map 添加到 List 中

**使用示例**

```java
JdbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "select * from student where age > 20";
List<Map<String, Object>> resultList = jtl.queryForList(sql);
for(Map<String, Object> result : resultList){
    System.out.println(result);
}
```

1. 将数据封装对象中

```java
// 将每一行的数据封装到对象中
public <T> List<T> query(String sql, RowMapper<T> rowMapper);
```

**参数说明**

- `RowMapper`: 是一个接口，可以自己实现，也可以使用 spring 实现的类`BeanPropertyRowMapper`

**使用示例**

- 自己实现`RowMapper`接口

```JAVA
JdbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "select * from student where age > 20";
List<Person> resultList = jtl.query(sql, new RowMapper<Person>() {

    @Override
    public Person mapRow(ResultSet resultSet, int i) throws SQLException {
        Person p = new Person();
        p.setAge(resultSet.getInt("age"));
        p.setName(resultSet.getString("name"));
        p.setScore(resultSet.getDouble("score"));
        return p;
    }
});

for(Person p: resultList){
    System.out.println(p);
}
```

- 使用 spring 实现的类

Person 类

```JAVA


import java.util.Date;

public class Person {
    private String name;
    private int age;
    private double score;
    private int id;
    private Date birthday;
    private Date insert_time;

    public String getName() {return name;}
    public void setName(String name) {this.name = name;}

    public int getAge() {return age;}
    public void setAge(int age) {this.age = age;}

    public double getScore() {return score;}
    public void setScore(double score) {this.score = score;}

    public int getId() {return id;}
    public void setId(int id) {this.id = id;}

    public Date getBirthday() {return birthday;}
    public void setBirthday(Date birthday) {this.birthday = birthday;}

    public Date getInsert_time() {return insert_time;}
    public void setInsert_time(Date insert_time) {this.insert_time = insert_time;}

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", score=" + score +
                ", id=" + id +
                ", birthday=" + birthday +
                ", insert_time=" + insert_time +
                '}';
    }
}
```

使用示例

```JAVA
JdbcTemplate jtl = new JdbcTemplate(JDBCUtilsStrong.getDataSource());
String sql = "select * from student where age > 20";
List<Person> resultList = jtl.query(sql, new BeanPropertyRowMapper<>(Person.class));
for(Person p: resultList){
    System.out.println(p);
}
```

**注意**

在设计类时，类的字段名要和数据库中的字段名相同，并且设置 getter 和 setter 方法,不然识别不了，

如果数据库中有的数据为 null ，在转换成 java 的基本数据类型时，会报不能赋值为 null 的错误。此时可以将基本的数据类型转换成对应的包装类以解决问题。