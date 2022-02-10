# MongoDB

## 什么是 MongoDB

MongoDB 是一个基于分布式文件存储的数据库，由 C++ 编写，旨在为 WEB 应用提供可扩展的高性能数据存储解决方案

是一个介于关系型数据库和非关系型数据库之间的产品，是非关系型数据库中功能最丰富，最像关系型数据库的，它支持的数据结构非常松散，是类似 json 的 bson 格式，因此可以存储比较复杂的数据类型， MongoDB 最大的特点就是它支持的查询语言非常强大，其语法特点有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引

MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 中存储的数据类似于 json 的对象，每个 key 对应的值，可以是数字，字符串或者数组。它是一个面向文档存储的数据库，操作起来比较简单和方便



## MongoDB 与 Mysql 数据库对比

| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| :----------- | :--------------- | :---------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |

在MongoDB数据库中名字空间 `<dbname>.system.*` 是包含多种系统信息的特殊集合(Collection)，如下:

| 集合命名空间                 | 描述                                      |
| :--------------------------- | :---------------------------------------- |
| `<dbname>.system.namespaces` | 列出所有名字空间。                        |
| `<dbname>.system.indexes`    | 列出所有索引。                            |
| `<dbname>.system.profile`    | 包含数据库概要(profile)信息。             |
| `<dbname>.system.users`      | 列出所有可访问数据库的用户。              |
| `<dbname>.local.sources`     | 包含复制对端（slave）的服务器信息和状态。 |

## 常用操作

| 命令                                       | 描述                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| `db`                                       | 显示当前使用的是哪一个数据库                                 |
| `show dbs`                                 | 显示所有的数据库                                             |
| `use <dbname>`                             | 使用 dbname 数据库,如果数据库不存在就创建数据库,此时如果去查是查不到的，因为只有数据库中有数据才会显示 |
| `db.dropDatabase()`                        | 删除当前正在使用的数据库，可以使用 `db` 命令查看当前正在使用的是哪个数据库 |
| `db.createCollection("<name>"[, options])` | 创建集合,其实可以不用创建集合，再插入数据时会如果集合不存在会自动创建集合 |
| `db.<collectionName>.drop()`               | 删除集合                                                     |
| `show tables / collections`                | 查看当前数据库中有哪些集合                                   |
| `db.<colName>.insert(document)`            | 往集合中插入数据，如果主键存在就会抛出异常，插入失败, 该方法在高版本已被启用，取而代之的是 insertOne(), insertMany() 方法 |
| `db.<colName>.find(), findOne()`           | 查看集合中的数据                                             |
| `db.<colName>.update()`                    | 更新文档                                                     |
| `db.<colName>.createIndex(keys, options)`  | 创建索引                                                     |
| `deleteOne, deleteMany, findOneAndDelete`  | 这三个方法都是用于删除集合中的数据                           |
| `db.<colName>.remove({})`                  | 清空 colName 集合中的数据                                    |

## MongoDB 中可以使用的类型

| **类型**                | **数字** | **别名**              | **备注**         |
| :---------------------- | :------- | --------------------- | :--------------- |
| Double                  | 1        | “double”              |                  |
| String                  | 2        | “string”              |                  |
| Object                  | 3        | “object”              |                  |
| Array                   | 4        | “array”               |                  |
| Binary data             | 5        | “binData”             |                  |
| Undefined               | 6        | “undefined”           | 已废弃。         |
| Object id               | 7        | “objectId”            |                  |
| Boolean                 | 8        | “bool”                |                  |
| Date                    | 9        | “date”                |                  |
| Null                    | 10       | “null”                |                  |
| Regular Expression      | 11       | “regex”               |                  |
| DBPointer               | 12       | “dbPointer”           | 已废弃           |
| JavaScript              | 13       | “javascript”          |                  |
| Symbol                  | 14       | “symbol”              | 已废弃           |
| JavaScript (with scope) | 15       | “javascriptWithScope” |                  |
| 32-bit integer          | 16       | “int”                 |                  |
| Timestamp               | 17       | “timestamp”           |                  |
| 64-bit integer          | 18       | “long”                | 3.4 版本之后出现 |
| Decimal128              | 19       | “decimal”             |                  |
| Min key                 | 255      | ”minKey”              | Query with `-1`. |
| Max key                 | 127      | “maxKey”              |                  |

在查询时，可以使用 `$type` 运算符判断数据的类型

### ObjectId

ObjectId 类似唯一主键，可以很快的去生成和排序，包含 12 bytes，含义是：

- 前 4 个字节表示创建 **unix** 时间戳,格林尼治时间 **UTC** 时间，比北京时间晚了 8 个小时
- 接下来的 3 个字节是机器标识码
- 紧接的两个字节由进程 id 组成 PID
- 最后三个字节是随机数

可以通过ObjectId 的 getTimestamp() 函数来获取文档的创建时间



## MongoDB 中的时间

使用 `new Date()` 和 `new ISODate()` 常见的日期对象，默认都是格林尼治时间,调用对象的 toString() 方法返回的就是本地时间或者直接使用 Date() 方法返回的也是本地时间

```shell
test> var date1 = new ISODate();
test> date1;
ISODate("2022-01-14T06:24:30.983Z")
test> var date2 = new Date();
test> date2
ISODate("2022-01-14T06:24:49.706Z")
test> date2.toString();
Fri Jan 14 2022 14:24:49 GMT+0800 (中国标准时间)
test> Date();
Fri Jan 14 2022 14:25:38 GMT+0800 (中国标准时间)
```

## MongoDb 中的比较

| 运算符 | 描述     |
| ------ | -------- |
| `$gt`  | 大于     |
| `$gte` | 大于等于 |
| `$lt`  | 小于     |
| `$lte` | 小于等于 |
| `$ne`  | 不等于   |

## MongoDB 中的 And 和 Or

MongoDB 中的 And 就是直接将查询条件使用 `,` 逗号分隔开，如 `{<queryCondition1>， <queryCondition2>....}` 

MongoDB 中的 Or 需要使用 `$or` 关键字，例如：`$or: [{<queryCondition1>}, {<queryCondition2>},.....]`

## 对结果排序

在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。

### 语法

sort()方法基本语法如下所示：

```
>db.COLLECTION_NAME.find().sort({KEY:1})
```

## 跳过数据以及查询指定数量的数据

跳过数据： skip(number) 跳过查询到的前 number 条数据

查询指定数量的数据：limit(number) 显示查询到的前 number 条数据



## 创建索引

### 语法

```shell
db.collection.createIndex(keys, options)
```

### options

| Parameter          | Type          | Description                                                  |
| :----------------- | :------------ | :----------------------------------------------------------- |
| background         | Boolean       | 建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" 可选参数。 "background" 默认值为**false**。 |
| unique             | Boolean       | 建立的索引是否唯一。指定为true创建唯一索引。默认值为**false**. |
| name               | string        | 索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称。 |
| dropDups           | Boolean       | **3.0+版本已废弃。**在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为 **false**. |
| sparse             | Boolean       | 对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 **false**. |
| expireAfterSeconds | integer       | 指定一个以秒为单位的数值，完成 TTL设定，设定集合的生存时间。 |
| v                  | index version | 索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。 |
| weights            | document      | 索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。 |
| default_language   | string        | 对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语 |
| language_override  | string        | 对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language. |

创建索引时，可以使用多个字段一起创建索引,例如：

```
db.values.createIndex({open: 1, close: 1}, {background: true})
```

1、看集合索引

```
db.col.getIndexes()
```

2、查看集合索引大小

```
db.col.totalIndexSize()
```

3、删除集合所有索引

```
db.col.dropIndexes()
```

4、删除集合指定索引

```
db.col.dropIndex("索引名称")
```

## 聚合(aggregate) 方法

在 MongoDB 中的聚合需要使用 aggregate()

```java
db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
{
   "result" : [
      {
         "_id" : "runoob.com",
         "num_tutorial" : 2
      },
      {
         "_id" : "Neo4j",
         "num_tutorial" : 1
      }
   ],
   "ok" : 1
}
```

相当于

```sql
select by_user, count(*) from mycol group by by_user
```

### 聚合表达式

| 表达式    | 描述                                                         | 实例                                                         |
| :-------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| $sum      | 计算总和。                                                   | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}]) |
| $avg      | 计算平均值                                                   | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}]) |
| $min      | 获取集合中所有文档对应值得最小值。                           | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}]) |
| $max      | 获取集合中所有文档对应值得最大值。                           | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}]) |
| $push     | 将值加入一个数组中，不会判断是否有重复的值。                 | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}]) |
| $addToSet | 将值加入一个数组中，会判断是否有重复的值，若相同的值在数组中已经存在了，则不加入。 | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}]) |
| $first    | 根据资源文档的排序获取第一个文档数据。                       | db.mycol.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}]) |
| $last     | 根据资源文档的排序获取最后一个文档数据                       | db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}]) |

同时，aggregate 方法中可以设置多个条件，用于多重筛选，类似于 linux 中的管道，一个条件执行的结果作为下一个条件的输入

## 整合到 Spring 应用中

pom.xml

```xml
<!---mongodb相关依赖-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

application.yaml

```yaml
mongodb:
  host: localhost # mongodb的连接地址
  port: 27017 # mongodb的连接端口号
  database: mall-port # mongodb的连接的数据库
```

### 常用注解

@Document：标识被修饰的类为 mongodb 集合中存储的数据对象

@Id: 标识 java 对象中的某个属性映射为 mongodb 中的 id 属性

@Indexed: 标识修饰的 java 对象中的属性为 mongodb 中的索引字段 

@Query: 可以使用 mongodb 中的 json 查询语句进行查询

### 操作 mongodb

继承 MongoRepository 接口可以获得常用的数据操作方法,且不需要实现对应的接口，Spring 帮我们实现好了，并且在启动应用程序时，会自动帮我们创建对应的对象，我们需要做的就是绑定就好了

```java
/**
 * 会员商品浏览历史Repository
 * Created by macro on 2018/8/3.
 */
public interface MemberReadHistoryRepository extends MongoRepository<MemberReadHistory,String> {
    /**
     * 根据会员id按时间倒序获取浏览记录
     * @param memberId 会员id
     */
    List<MemberReadHistory> findByMemberIdOrderByCreateTimeDesc(Long memberId);
    
}
```

