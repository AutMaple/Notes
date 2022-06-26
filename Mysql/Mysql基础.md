# Mysql数据库基础

## Binary strings(二进制串)

二进制字符串是由字节序列组成。与包含文本数据的字符串不同，一个二进制字符串通常用于保存非传统的数据，如图片。一个二进制字符串的长度等于序列中包含的字节数。A binary string has a CCSID of 65535. Only character strings of FOR BIT DATA are compatible with binary strings.

## Mysql 数据类型

### 数值类型

| 类型         | 大小                                     | 范围（有符号）                                               | 范围（无符号）                                               | 用途            |
| :----------- | :--------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :-------------- |
| TINYINT      | 1 字节                                   | (-128，127)                                                  | (0，255)                                                     | 小整数值        |
| SMALLINT     | 2 字节                                   | (-32 768，32 767)                                            | (0，65 535)                                                  | 大整数值        |
| MEDIUMINT    | 3 字节                                   | (-8 388 608，8 388 607)                                      | (0，16 777 215)                                              | 大整数值        |
| INT或INTEGER | 4 字节                                   | (-2 147 483 648，2 147 483 647)                              | (0，4 294 967 295)                                           | 大整数值        |
| BIGINT       | 8 字节                                   | (-9 233 372 036 854 775 808，9 223 372 036 854 775 807)      | (0，18 446 744 073 709 551 615)                              | 极大整数值      |
| FLOAT        | 4 字节                                   | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)                  | 单精度 浮点数值 |
| DOUBLE       | 8 字节                                   | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
| DECIMAL      | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值                                               | 依赖于M和D的值                                               | 小数值          |

| Data type                     | Description                                                  |
| :---------------------------- | :----------------------------------------------------------- |
| BIT(*size*)                   | A bit-value type. The number of bits per value is specified in *size*. The *size* parameter can hold a value from 1 to 64. The default value for *size* is 1. |
| TINYINT(*size*)               | A very small integer. Signed range is from -128 to 127. Unsigned range is from 0 to 255. The *size* parameter specifies the maximum display width (which is 255) |
| BOOL                          | Zero is considered as false, nonzero values are considered as true. |
| BOOLEAN                       | Equal to BOOL                                                |
| SMALLINT(*size*)              | A small integer. Signed range is from -32768 to 32767. Unsigned range is from 0 to 65535. The *size* parameter specifies the maximum display width (which is 255) |
| MEDIUMINT(*size*)             | A medium integer. Signed range is from -8388608 to 8388607. Unsigned range is from 0 to 16777215. The *size* parameter specifies the maximum display width (which is 255) |
| INT(*size*)                   | A medium integer. Signed range is from -2147483648 to 2147483647. Unsigned range is from 0 to 4294967295. The *size* parameter specifies the maximum display width (which is 255) |
| INTEGER(*size*)               | Equal to INT(size)                                           |
| BIGINT(*size*)                | A large integer. Signed range is from -9223372036854775808 to 9223372036854775807. Unsigned range is from 0 to 18446744073709551615. The *size* parameter specifies the maximum display width (which is 255) |
| FLOAT(*size*, *d*)            | A floating point number. The total number of digits is specified in *size*. The number of digits after the decimal point is specified in the *d* parameter. This syntax is deprecated in MySQL 8.0.17, and it will be removed in future MySQL versions |
| FLOAT(*p*)                    | A floating point number. MySQL uses the *p* value to determine whether to use FLOAT or DOUBLE for the resulting data type. If *p* is from 0 to 24, the data type becomes FLOAT(). If *p* is from 25 to 53, the data type becomes DOUBLE() |
| DOUBLE(*size*, *d*)           | A normal-size floating point number. The total number of digits is specified in *size*. The number of digits after the decimal point is specified in the *d* parameter |
| DOUBLE PRECISION(*size*, *d*) |                                                              |
| DECIMAL(*size*, *d*)          | An exact fixed-point number. The total number of digits is specified in *size*. The number of digits after the decimal point is specified in the *d* parameter. The maximum number for *size* is 65. The maximum number for *d* is 30. The default value for *size* is 10. The default value for *d* is 0. |
| DEC(*size*, *d*)              | Equal to DECIMAL(size,d)                                     |

### 日期和时间类型

| 类型      | 大小 (字节) | 范围                                                         | 格式                | 用途                     |
| :-------- | :---------- | :----------------------------------------------------------- | :------------------ | :----------------------- |
| DATE      | 3           | 1000-01-01/9999-12-31                                        | YYYY-MM-DD          | 日期值                   |
| TIME      | 3           | '-838:59:59'/'838:59:59'                                     | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1           | 1901/2155                                                    | YYYY                | 年份值                   |
| DATETIME  | 8           | 1000-01-01 00:00:00/9999-12-31 23:59:59                      | YYYY-MM-DD HH:MM:SS | 混合日期和时间值         |
| TIMESTAMP | 4           | 1970-01-01 00:00:00/2038 结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038-1-19 03:14:07 | YYYYMMDD HHMMSS     | 混合日期和时间值，时间戳 |

| Data type        | Description                                                  |
| :--------------- | :----------------------------------------------------------- |
| DATE             | A date. Format: YYYY-MM-DD. The supported range is from '1000-01-01' to '9999-12-31' |
| DATETIME(*fsp*)  | A date and time combination. Format: YYYY-MM-DD hh:mm:ss. The supported range is from '1000-01-01 00:00:00' to '9999-12-31 23:59:59'. Adding DEFAULT and ON UPDATE in the column definition to get automatic initialization and updating to the current date and time |
| TIMESTAMP(*fsp*) | A timestamp. TIMESTAMP values are stored as the number of seconds since the Unix epoch ('1970-01-01 00:00:00' UTC). Format: YYYY-MM-DD hh:mm:ss. The supported range is from '1970-01-01 00:00:01' UTC to '2038-01-09 03:14:07' UTC. Automatic initialization and updating to the current date and time can be specified using DEFAULT CURRENT_TIMESTAMP and ON UPDATE CURRENT_TIMESTAMP in the column definition |
| TIME(*fsp*)      | A time. Format: hh:mm:ss. The supported range is from '-838:59:59' to '838:59:59' |
| YEAR             | A year in four-digit format. Values allowed in four-digit format: 1901 to 2155, and 0000. MySQL 8.0 does not support year in two-digit format. |

### 字符串类型

| 类型       | 大小                | 用途                            |
| :--------- | :------------------ | :------------------------------ |
| CHAR       | 0-255字节           | 定长字符串                      |
| VARCHAR    | 0-65535 字节        | 变长字符串                      |
| TINYBLOB   | 0-255字节           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255字节           | 短文本字符串                    |
| BLOB       | 0-65 535字节        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535字节        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215字节    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215字节    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295字节 | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295字节 | 极大文本数据                    |

| Data type                   | Description                                                  |
| :-------------------------- | :----------------------------------------------------------- |
| CHAR(size)                  | A FIXED length string (can contain letters, numbers, and special characters). The *size* parameter specifies the column length in characters - can be from 0 to 255. Default is 1 |
| VARCHAR(size)               | A VARIABLE length string (can contain letters, numbers, and special characters). The *size* parameter specifies the maximum column length in characters - can be from 0 to 65535 |
| BINARY(size)                | Equal to CHAR(), but stores binary byte strings. The *size* parameter specifies the column length in bytes. Default is 1 |
| VARBINARY(size)             | Equal to VARCHAR(), but stores binary byte strings. The *size* parameter specifies the maximum column length in bytes. |
| TINYBLOB                    | For BLOBs (Binary Large OBjects). Max length: 255 bytes      |
| TINYTEXT                    | Holds a string with a maximum length of 255 characters       |
| TEXT(size)                  | Holds a string with a maximum length of 65,535 bytes         |
| BLOB(size)                  | For BLOBs (Binary Large OBjects). Holds up to 65,535 bytes of data |
| MEDIUMTEXT                  | Holds a string with a maximum length of 16,777,215 characters |
| MEDIUMBLOB                  | For BLOBs (Binary Large OBjects). Holds up to 16,777,215 bytes of data |
| LONGTEXT                    | Holds a string with a maximum length of 4,294,967,295 characters |
| LONGBLOB                    | For BLOBs (Binary Large OBjects). Holds up to 4,294,967,295 bytes of data |
| ENUM(val1, val2, val3, ...) | A string object that can have only one value, chosen from a list of possible values. You can list up to 65535 values in an ENUM list. If a value is inserted that is not in the list, a blank value will be inserted. The values are sorted in the order you enter them |
| SET(val1, val2, val3, ...)  | A string object that can have 0 or more values, chosen from a list of possible values. You can list up to 64 values in a SET list |

## SQL通用语法

1. SQL 语句可以单行或多行书写，以分号结尾
2. 可以使用空格和缩进来增强 SQL 语句的可读性
3. MYSQL 数据库的 SQL 语句不区分大小写，但是关键字建议使用大写
4. SQL 的三种注释方法
   - 单行注释： 使用`--` 或者 `#` ( MYSQL独有的注释方法 )进行注释，注意，注释符号后空一格，然后书写注释语句
   - 多行注释： `/* 注释内容... */`

- 

## SQL 分类

1. DDL(Data Definition Language) 数据定义语言

   用于定义数据对象：数据库，表，列等

2. DML(Data Manipulation Language) 数据操作语言

   用于对数据库中的数据进行增删改等操作

3. DQL(Data Query Language) 数据查询语言

   用于查询数据库中的数据

4. DCL(Data Control Language) 数据控制语言

   定义数据库的访问权限和安全级别以及创建用户等

## mysql常用语句

### 常用数据类型

| 类型        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| `double`    | 小数: `double(4,1)`，一共 4 位，小数点后 1 位                |
| `date`      | 日期，格式：`yyyy-MM-dd`                                     |
| `datetime`  | 日期，格式：`yyyy-MM-dd HH:mm:ss`                            |
| `timestamp` | 时间戳，格式: `yyyy-MM-dd` ，插入数据时自动将系统时间作为默认值 |
| `int`       | 整数                                                         |
| `varchar`   | 字符串类型：`varchar(<length>)`， 最大存储`length`个字符     |

### database相关的操作

```sql
-- 查询所有的数据库
1. show databases; 

-- 查看数据库相关的信息(创建数据库所用的语句)
2. show create database <dbName> 

-- 创建一个 dbName 的数据库，如果数据库存在则报错
3. create database <dbName> 

-- 数据库<dbName>不存在就创建，否则不创建
4. create database if not exists <dbName> 

 -- 使用指定的字符集创建数据库
5. create database <dbName> character set <setName>

 -- 修改数据库使用的字符集
6. alter database <dbName> character set <setName>

-- 删除数据库，如果数据库不存在，则会报错
7. drop database <dbName> 

 -- 数据库存在才删除
8. drop database if exists <dbName>

 -- 查询当前正在使用的数据库
9. select database()

-- 使用数据库
10. use <dbName> 
```

### table相关的操作

```sql
-- 查看数据库中所有存在的表
1. show tables; 

-- 查看表各字段的属性
2. desc <tableName> 

 -- 创建表
3. create table <tableName>(
	<colName> type,
    <colName> type,
    ....
    <colName> type -- 最后一列不需要在结尾加上逗号
); 

-- 创建一个和existTableName一样的表
create table <tableName> like <existTableName>

 -- 删除表，表不存在则回报错
4. drop table <tableName>

-- 如果表存在就删除
5. drop table if exists <tableName> 

 -- 修改表名
6. alter table <oldName> rename to <newName>

-- 查看表的属性
7. show create table <tableName> 

 -- 修改表的字符集
8. alter table <tableName> character set <setName>

-- 给表添加一个字段(列)
9. alter table <tableName> add <colName> <type> 

-- 同时修改列名和类型
10. alter table <tableName> change <oldColName> <newColName> <type> 

-- 只修改类型
11. alter table <tableName> modify <colName> <type>

-- 删除列
12. alter table <tableName> drop <colName>


-- --------------------- 对表中的数据增删改查 ------------------------
-- 查询表中的所有数据
13. selec * from <tabeleName>

-- 查询表中特定列的数据
14. select <col1>,<col2>,...,<colN> from <tableName>

-- 插入数据
15. insert into <tableName>[(col1,col2,...)] values (value1,value2,...)
-- 注意：1. 列名和插入的值相对应 2. 如果想给每一列都添加数据，列名可以省略

-- 删除数据
16. delete from <tableName> [where <condition> ] 
-- 注意：如果不写 condition 将会删除表中全部的数据，不推荐使用该方法删除表中所有数据

-- 清空表
17. truncate table <tableName>
-- 该命令执行的操作是先删除表中所有的数据，然后再创建一个一模一样的空表

-- 修改数据
18. update <tableName> set <colName> = <val1>, <colName> = <val2> [where <condition> ]
-- 如果不加 condition，将会修改表中所有的数据
```

**注意**

mysql 的默认值只有在 insert 语句中没有这个字段时才会生效，如果 insert 中有插入该字段而该字段取值又为 null ，null 值将被插入到表中。如果 insert 语句中有需要填充默认值的字段，但是又不想插入 null 值，此时可以在对应的位置填入`default`关键字，这样存储在数据库中的数据就是对应字段的默认值。

```SQL
create table student(
	id int,
    name varchar(32),
    age int,
    score double(4,1),
    birthday date,
    insert_time timestamp default current_timestamp
);
```

student 表的定义。向表中插入数据

```SQL
1. insert into student values(1,"Irvin", 27, 99, "1994-07-06",DEFAULT); -- insert_time字段将插入默认值
2. insert into student values(1,"Irvin", 27, 99, "1994-07-06",NULL); -- insert_time字段将插入NULL
-- 3. insert into student values(1,"Irvin", 27, 99, "1994-07-06"); -- 这个语句将会报错，插入失败
```

## 查询语句

```SQL
-- 排序方式
1. order by <field>,<field2>,... [ASC(default)| DESC ]
-- ASC 升序方式 DESC 降序方式,其中只有第一个字段相同时，才比较第二个字段，以此类推
```

语法：

```SQL
select
	字段列表
from 
	表名列表
where
	条件列表
group by
	分组字段
having
	-- having 子句出现的数据必须在 group by 执行之后,在 having 中可以使用聚合函数
	分组之后的条件 
order by
	排序
limit
	分页显示
```

**注意**

mysql 中 utf8 编码格式的默认排序规则就是：utf8_general_ci ===> 不区分大小写。因此在创建表的时候分清清楚字段需不需要区分大小写

可以使用子查询返回单个值与 where 语句中的比较运算符进行比较



### 聚合函数

| 函数  | 作用                                        |
| ----- | ------------------------------------------- |
| count | 返回查询到多少条数据，不计算值为 null 的 列 |
| max   | 计算最大值                                  |
| min   | 计算最小值                                  |
| sum   | 计算和                                      |
| avg   | 计算平均值                                  |

### 常用函数

| isnull() | 判断是否为空 |
| -------- | ------------ |
|          |              |
|          |              |
|          |              |

**注意**

聚合函数会排除 null 的值，如果想包含值为 null 列的行，可以计算非空的列或者使用`IFNULL(field, newValue)`,该函数的意思是：如果 `filed`列某个值为 null，就将

null 修改为 newValue;

## where 和 having 的区别

- where 在分组之前进行限定，如果不满足条件，则不参与分组。having 在分组之后进行限定，如果不满足条件，则不会被查询出来
- where 后不可以跟聚合函数，having 可以进行进行聚合函数的判断

## limit 关键字

```SQL
limit 0,5 -- 表示显示第 1，2，3，4，5 条数据

limit 5,5 -- 表示显示第 6，7，8，9，10 条数据
```

## hash 连接

hash 连接的步骤是将内表(子查询出来的表)中的每一行 hash 到内存中，然后将主表的每一行进行 hash 于内表的 hash 值进行比较，选出 hash 值相同的

## loop 循环

loop 循环指的是将主表中的数据每一条和内表中的数据进行匹配，不过内表需要查询数据库，但是查数据库时能够使用索引，因此比较的次数取决于主表的行数，主表有 10000 行，就得查 10000 次数据库 

## in 和 exists 的区别

1. exists、not exists 一般都是与子查询一起使用，In 可以与子查询一起使用，也可以直接in (a,b.....)

2. **exists 会针对子查询的表使用索引，not exists 会对主子查询都会使用索引。in 与子查询一起使用的时候，只能针对主查询使用索引，not in 则不会使用任何索引**。

**注意**

一直以来认为 exists 比 in 效率高的说法是不准确的。

in 是把外表和内表作 hash 连接，而 exists 是对外表作 loop 循环，每次 loop 循环再对内表进行查询。

- 如果查询的两个表大小相当，那么用 in 和 exists 差别不大。
- 如果两个表中一个较小，一个是大表，则子查询表大的用 exists，子查询表小的用 in

in 是在内存中进行比较，而 exists 是通过查数据库的方式进行查找

IN 的元素不能超过1000个。

所有的[关联查询](https://so.csdn.net/so/search?q=关联查询&spm=1001.2101.3001.7020)都可以转换为子查询。但是并不是所有的子查询都能转化成关联查询。

## not in 和 not exists

- 如果查询语句使用了 not in 那么内外表都进行全表扫描，没有用到索引
- 如果查询语句使用了 not extsts， 那么子查询依然能用到子表上的索引。

因为 not in 实质上等于 `!= and != ...`，因为 != 不会使用索引，故 not in 不会使用索引。

所以**无论那个表大，用 not exists 都比 not in 要快**。

## 约束

- 主键约束 – primary key 非空且唯一，一张表只能有一个主键
- 非空约束 – nut null
- 唯一约束 – unique 唯一约束的列可以有多个 null 值
- 外键约束 – foreign key
- 自动增长 – auto_increment 自动读取上一条字段的值，然后在其基础上自增一

### 删除唯一约束

```SQL
alter table <tableName> drop index <colName>
```

### 删除主键约束

```SQL
alt table <tableName> drop primary key
```

主键只有一个，因此不用指定列名

### 外键约束

```SQL
create table <tableName>(
	......
    外键列名称,
    constraint <foreignKeyName> foreign key (<colName>) references <refTableName>(<refColName>)
)
```

例如：

```SQL
constraint emp_dept_fk foreign key (dep_id) references department(id)
```

#### 删除外键

```SQL
alter table <tableName> drop foreign key <keyName>
```

#### 添加外键

```SQL
alter table <tableName> add constraint <foreignKeyName> foreign key (<colName>) references <refTableName>(<refColName>)
```

#### 级联操作

在创建外键的时在最后加上 `on update cascade`

```SQL
constraint <foreignKeyName> foreign key (<colName>) references <refTableName>(<refColName>) on update cascade on delete cascade
```

更新级联操作和删除级联操作(**谨慎使用**)

## 设计方式

一对一的关系：在任意一方设置唯一外键指向另一方主键（很少用），因此可以合并在一张表中

多对一的关系：在多的一方设置外键引用一的主键

多对多的关系: 需要设置第三方表来实现，第三章表至少有两个字段，分别是各表的主键。

## 数据库设计范式

设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。

目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式]（5NF，又称完美范式）。

- 函数依赖：A –> B, 如果通过 A 属性( 属性组 ) 的值可以唯一确定 B 属性的值，则称 B 依赖于 A

  例如：学号 –> 姓名 （学号,课程）–> 分数

- 完全函数依赖：A –> B, 如果 A 是一个属性组，则 B 属性的值的确定需要依赖 A 属性组中所有的属性值

  例如: （学号,课程）–> 分数

- 部分函数依赖： A –> B, 如果 A 是一个属性组， 则 B 属性值的确定只需要依赖 A 属性组中部分属性即可

  例如： (学号，课程) –> 姓名

- 传递函数依赖： A – > B , B –> C， 如果通过 A 属性（属性组）的值，可以确定唯一 B 的值，在通过 B 属性（属性组）的值可以唯一确定 C 属性的值，则称 C 传递函数依赖于 A

  例如：学号 –> 系名， 系名 –> 系主任 ==> 学号 –> 系主任

- 码： 如果一张表中，一个属性或属性组，被其他所有属性所依赖，则称这个属性（属性值）为该表的码。

  例如：（学号,课程名称），表中的所有属性都部分或完全函数依赖这个码来确定

## 数据库的备份和还原

```SHELL
# 命令行方式
## 备份

$ mysqldump -u <username> -p <password> <databaseName> > <savePath>

## 还原
### 1. 登陆数据库
### 2. 创建数据库
### 3. 使用数据库
### 4. 执行备份文件 --> source <filePath>
```

## 多表查询

from 子句中 on 条件主要用来连接表，其他不属于连接表的条件可以使用 where 子句来指定； 

join 连接分为三种: 其中外连接的 outer 和内链接的 inner 可以省略

1. 内连接: inner join, 默认，所以可以省略 inner 关键字。要求两个表中的值都存在，即取两个表中的交集

2. 外连接: 附表中的值可能会被置为 null

   - left outer join, 左外连接，结果表中除了匹配的行外，还包括左表中有而右表中不匹配的行，右表中不存在的行置为 null

   - right outer join， 右外连接，结果表中除了匹配的行外，还包括右表中有而左表中不匹配的行，左表中不存在的行置为 null

3. 交叉连接：cross join，交叉连接，实际上就是将两个表进行笛卡尔积运算，结果表的行数等于两表行数之积

- 内连接查询
  - 隐式内连接
  - 显示内连接
- 外连接查询
  - 左外连接
  - 右外连接
- 子查询
  - 子查询结果是单行单列的将其当作变量使用
  - 子查询结果是多行单列的将其当作数组使用，可以使用 in 关键字进行使用
  - 子查询结果是多行多列的数据,将其作为一个虚拟表来使用

```sql
-- 关键字 on 主要用于连接表,
select
	FirstName, LastName, City, State
from
	Person left join Address
on
	Person.PersonId = Address.PersonId;
```



## 事务

一个包含一个或多个操作的集合，这些操作要么同时成功，要么同时失败。如果一个事务执行中途被打断，则会进行回滚操作，回到未进行本次事务的初始状态。

mysql 默认自动提交事务，执行一句 sql 语句，提交一次事务。

如果想手动设置事务，需要执行 `start transaction` 指令

### 四大特性

- 原子性： 是不可分割的最小单位，要么同时成功，要么同时失败
- 持久性：当时提交或回滚后，数据库会持久化的保存数据
- 隔离性：多个事务之间相互独立
- 一致性：实务操作前后，数据总量不变

### 事务的隔离级别

- 概念：多个事务之间是相互独立、隔离的。但如果多个事务操作同一批数据，则会引发一些问题。设置不同的隔离级别可以解决这些问题

- 存在的问题：

  1. 脏读：一个事务，读取到另一个事务还没有提交的数据
  2. 不可重复读（虚读）: 同一个事务中，两次读的数据不一样
  3. 幻读：一个事务操作数据表中的所有纪律，另一个事务添加了一条数据，则第一个事务查询不到自己的修改

- 隔离级别

  - read uncommited: 读未提交
    - 产生的问题：脏读、不可重复读、幻读
  - read commited： 读已提交
    - 产生的问题： 可不重复读、幻读
  - repeatable read: 可重复读(Mysql默认)
    - 产生的问题： 幻读
  - serializable: 串行化，可以解决所有的问题,本质上是对表进行上锁

  隔离级别从小到大安全性越来越高，但是效率越来越底

- 查询隔离级别：

  - `select @@tx_isolation`

- 设置隔离级别：

  - `set global transaction isolation level <level>`

## 查看默认的提交方式

```SQL
select @@autocommit
```

#### 修改默认提交方式

```SQL
set @@autocommit = 0;
```

### 开启事务

```SQL
start transaction   
```

### 回滚事务

```SQL
rollback
```

### 提交

将事务执行的最终结果进行提交,提交事务之后，本次事务执行结束

```SQL
commit
```

## 用户管理

### 查询用户

```SQL
use mysql;
select * from user;
```

### 创建用户

```SQL
create user '<username>'@'<hostname>' identified by '<password>'

-- 例子
create user 'irvin'@'localhost' identified by '121212'
create user 'supper'@'%' identified by '123456' -- % 是一个通配符，表示任何一台主机都可以以 supper 用户进行登录
```

### 删除用户

```SQL
drop user '<username>'@'<hostname>'
```

### 修改用户密码

```SQL
-- 登陆 mysql 数据库之后执行
use mysql

-- 第一种方式
update user set password = password('<newPassword>') where user='<username>'

-- 第二种方式
set password for '<username>'@'<hostname>' = password('<newPassword>')
```

### 用户权限管理

#### 查询权限

```SQL
show grants for '<username>'@'<hostname>' -- hostname 可以使用通配符
```

#### 授予权限

```SQL
grant 权限列表 on <database>.<tableName> to '<username>'@'<hostname>'

-- 给予全部权限
grant all on '*.*' to '<username>'@'<hostname>'
```

权限列表可以查询 root 用户的权限

```SQL
show grants for 'root'@'localhost'
```

#### 撤销权限

```SQL
revoke 权限列表  on <database>.<tableName> to '<username>'@'<hostname>'
```

## 注意事项

from 中的表如果是子查询生成的表，则必须为子查询指定一个表名，否则会报错

```sql
select Email
from 
(
    select Email, count(Email) as num
    from Person
    group by Email
) as statistic 
where num > 1;

-- 更加高效的方式
select Email
from Person
group by Email
having count(Email) > 1;
```

子查询中不要写上分号 `;`，否则会报错

```sql
select name
from customers
where id not in
(
    select customerid from orders -- `;` 这个分号不要写啊，会报错的
);
```

