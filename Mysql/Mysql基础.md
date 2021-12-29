# Mysql数据库基础

## SQL通用语法

1. SQL 语句可以单行或多行书写，以分号结尾
2. 可以使用空格和缩进来增强 SQL 语句的可读性
3. MYSQL 数据库的 SQL 语句不区分大小写，但是关键字建议使用大写
4. SQL 的三种注释方法
   - 单行注释： 使用`--` 或者 `#` ( MYSQL独有的注释方法 )进行注释，注意，注释符号后空一格，然后书写注释语句
   - 多行注释： `/* 注释内容... */`

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
	分组之后的条件
order by
	排序
limit
	分页显示
```

**注意**

mysql 中 utf8 编码格式的默认排序规则就是：utf8_general_ci ===> 不区分大小写。因此在创建表的时候分清清楚字段需不需要区分大小写

### 聚合函数

| 函数  | 作用                                        |
| ----- | ------------------------------------------- |
| count | 返回查询到多少条数据，不计算值为 null 的 列 |
| max   | 计算最大值                                  |
| min   | 计算最小值                                  |
| sum   | 计算和                                      |
| avg   | 计算平均值                                  |

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