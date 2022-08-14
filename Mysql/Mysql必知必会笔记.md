# 常用命令

| 命令                                                | 描述                      |
| --------------------------------------------------- | ------------------------- |
| `show databases`                                    | 显示所有的数据库          |
| `show columns from <table>` 或者 `describe <table>` | 显示名为 table 表的列信息 |
| `show status`                                       | 展示服务器的状态          |
| `show create <database>`                            | 展示创建数据库时的信息    |
| `show grants`                                       | 展示授权信息              |
| `show errors`                                       | 展示服务器的错误信息      |
| `show warnings`                                     | 展示服务器的警告信息      |
| `help show`                                         | 查看 show 命令的使用方法  |
| `show character set`                                | 查看数据库支持的字符集    |
| `show collation`                                    | 查看所有字符集的校对规则  |
| `show table status like '<tableName>' \G`           | 查看 tableName 表的信息   |

# Select 命令

## distinct 关键字

distinct 关键字必须放在所有列名的前面，该关键不能够只对单独的列使用，如

```sql
SELECT DISTINCT vend_id, prod_price FROM products;
```

则表示 vend_id 和 prod_price 都不相同时，才会被放入到结果集中

## limit 关键字

limit 关键字用于设置结果集中的行数, 语法：`LIMIT <offset>, <count>`, 表示从 offset 开始，选出 count 行。如果 offset 省略，则表示从第一行开始，选则 count 行。

> 注意：offset 从 0 开始

```
SELECT vend_id, prod_price FROM products LIMIT 5, 4;
```

表示从第 6 行开始，选择 4 行，即 6，7，8, 9 行被放入到结果集中

# and 和 or 逻辑运算符

在 Sql 中 and 运算符的优先级要比 or 的优先级高。例如：

```sql
SELECT prod_name, prod_price
FROM products
WHERE vend_id = 1002 OR vend_id = 1003 and prod_price >= 10;
```

这条语句与下面的这条语句等价

```sql
SELECT prod_name, prod_price
FROM products
WHERE vend_id = 1002 OR (vend_id = 1003 and prod_price >= 10);
```

即 vend_id = 1003 且 prod_price >= 10 的产品或者 vend_id = 1002 的产品才会被选出来

# IN 与 OR 的比较

- 在使用长的合法选项清单时，IN 操作符的语法更清楚且更直观。
- 在使用 IN 时，计算的次序更容易管理（因为使用的操作符更少）。
- **IN 操作符一般比 OR 操作符清单执行更快**。
- IN 的最大优点是可以包含其他 SELECT 语句，使得能够更动态地建立 WHERE 子句。

# 通配符

| 通配符 | 描述                                            |
| ------ | ----------------------------------------------- |
| %      | 表示任意字符出现任意次, 该通配符不能够匹配 NULL |
| _      | 下划线用于匹配任意的单个字符                    |

## 使用通配符的建议

- 不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。

- 在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。**把通配符置于搜索模式的开始处，搜索起来是最慢的**。
- 仔细注意通配符的位置。如果放错地方，可能不会返回想要的数

# 正则表达式

在 sql 中使用正则表达式需要使用 REGEXP 关键字

## REGEXP 和 LIKE 的区别

LIKE 是将单元格中的整个内容和 LIKE 的表达式相比，但是 REGEXP 则是对列中的内容逐个匹配，例如：

有一个单元格的内容为 aaa10000, 则 `LIKE '10000'` 则不能够匹配这个单元格的内容，但是 `REGEXP '10000'` 可以匹配这个单元格的内容

LIKE 匹配整个列。如果被匹配的文本是值的一部分，LIKE 会匹配失败，相应的行也不被返回（除非使用通配符）。而 REGEXP 在列值内进行匹配，如果被匹配的文本在列值中出现，REGEXP 会匹配成功，相应的行将被返回。

> Mysql 中正则表达式匹配不区分大小系，如果需要区分大小写，需要使用 BINARY 关键字，如 WHERE prod_name REGEXP BINARY 'JetPack .000'

## 转义字符

在 Mysql 正则表达式中使用 `\\` 来匹配特殊字符，例如要匹配 `.` 字符，需要使用 `\\.` 才能够正确的进行匹配，为了匹配 `\` 则需要使用 `\\\`

> 多数正则表达式实现使用单个反斜杠转义特殊字符，以便能使用这些字符本身。但 MySQL 要求两个反斜杠（MySQL 自己解释一个，正则表达式库解释另一个）

## 匹配字符类

| 字符类     | 描述                                              |
| ---------- | ------------------------------------------------- |
| [:alnum:]  | 任意字母和数字（同[a-zA-Z0-9]）                   |
| [:alpha:]  | 任意字符（同[a-zA-Z]）                            |
| [:blank:]  | 空格和制表（同[\\t]）                             |
| [:cntrl:]  | ASCII 控制字符（ASCII 0到31和127）                |
| [:digit:]  | 任意数字（同[0-9]）                               |
| [:graph:]  | 与[:print:]相同，但不包括空格                     |
| [:lower:]  | 任意小写字母（同[a-z]）                           |
| [:print:]  | 任意可打印字符                                    |
| [:punct:]  | 既不在[:alnum:]又不在[:cntrl:]中的任意字符        |
| [:space:]  | 包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]） |
| [:upper:]  | 任意大写字母（同[A-Z]）                           |
| [:xdigit:] | 任意十六进制数字（同[a-fA-F0-9]                   |

使用示例

```sql
SELECT prod_name
FROM products
WHERE prod_name REGEXP '[[:digit:]]{4}'
```

## 定位元字符

| 元字符  | 描述       |
| ------- | ---------- |
| ^       | 文本的开头 |
| $       | 文本的结尾 |
| [[:<:]] | 单词的开始 |
| [[:>:]] | 单词的结尾 |

> 可以在不适用数据库表的情况下用 SELECT 语句来测试正则表达式，REGEXP 检查总是返回 0(没有匹配)或 1(匹配)。可以用带文字串的 REGEXP 来测试表达式，并试验它们。语法：`select 'hello' regexp '[[:digit:]]'`

# 常用的函数

## 字符串处理函数

| 函数              | 描述                                          |
| ----------------- | --------------------------------------------- |
| concat(object...) | 拼接字符串,可以传递任意多个参数和列值进行拼接 |
| rtrim()           | 删除字符串右边的空格                          |
| ltrim()           | 删除字符串左边的空格                          |
| trim()            | 删除字符串两边的空格                          |
|                   |                                               |
| upper()           | 转换成大写                                    |
| left()            | 返回串左边的字符                              |
| length()          | 返回串的长度                                  |
| locate()          | 找出串的一个字窜                              |
| lower()           | 转换成小写字符                                |
| right()           | 返回串右边的字符                              |
| soundex()         | 返回字串的 soundex 值                         |
| substring()       | 返回字符串的子串                              |

## 日期时间处理函数

| 函数          | 描述                             |
| ------------- | -------------------------------- |
| now()         | 返回当前的日期和时间             |
| addDate()     | 增加日期                         |
| addTime()     | 增加时间                         |
| curDate()     | 返回当前的日期                   |
| curTime()     | 返回当前的时间                   |
| Date()        | 返回日期时间的日期部分           |
| DateDiff()    | 计算两个日期之差                 |
| date_add()    | 高度灵活的日期运算函数           |
| data_format() | 返回一个格式化之后的日期或时间串 |
| day()         | 返回一个日期的天数部分           |
| dayOfWeek()   | 返回日期对应的星期               |
| hour()        | 返回时间的小时部分               |
| minute()      | 返回时间的分钟部分               |
| month()       | 返回日期的月份部分               |
| second()      | 返回时间的秒部分                 |
| time()        | 返回一个日期时间的时间部分       |
| year()        | 返回一个日期的年份部分           |

> 无论你什么时候指定一个日期，不管是插入或更新表值还是用 WHERE 子句进行过滤，日期必须为格式 yyyy-mm-dd

## 数值处理函数

| 函数   | 说明       |
| ------ | ---------- |
| abs()  | 绝对值     |
| cos()  | 余弦       |
| exp()  | 返回指数值 |
| mod()  | 取余       |
| pi()   | 返回圆周率 |
| rand() | 返回随机数 |
| sin()  | 返回正弦   |
| sqrt() | 返回平方根 |
| tan()  | 返回正切   |

## 聚集函数

| 函数    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| avg()   | 返回某列的平均值,忽略 NULL 值                                |
| count() | 返回某列的行数, count(*) 则包括值为 NULL 行，count(colName) 则不包括值为 NULL 的行 |
| max()   | 返回某列的最大值                                             |
| min()   | 返回某列的最小值                                             |
| sum()   | 返回某列的和                                                 |

# 分组 Group By

Group By 相关规定：

- GROUP BY 子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。
- 如果在 GROUP BY 子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。
- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式（但不能是聚集函数）。如果在 SELECT 中使用表达式，则必须在
  GROUP BY 子句中指定相同的表达式。不能使用别名。
- **除聚集计算语句外，SELECT 语句中的每个列都必须在 GROUP BY 子句中给出。**
- 如果分组列中具有 NULL 值，则 NULL 将作为一个分组返回。如果列中有多行 NULL 值，它们将分为一组。
- GROUP BY子 句必须出现在 WHERE子 句之后，ORDER BY 子句之前。

# where 和 having

- where 和 having 的区别就是 where 过滤的是行，having 过滤的是分组
- WHERE 在数据分组前进行过滤，HAVING 在数据分组后进行过滤

# 组合查询 Union

MySQL 允许执行多个查询(多条 SELECT 语句)，并将结果作为单个查询结果集返回。这些组合查询通常称为并(union)或复合查询(compound query)。

有两种基本情况，其中需要使用组合查询：

- 在单个查询中从不同的表返回类似结构的数据；
- 对单个表执行多个查询，按单个查询返回数据。

任何具有多个 WHERE 子句的 SELECT 语句都可以作为一个组合查询给出

## Union 的规则

- UNION 必须由两条或两条以上的 SELECT 语句组成，语句之间用关键字 UNION 分隔，因此，如果组合 4 条 SELECT 语句，将要使用 3 个 UNION 关键字
- UNION 中的每个查询必须包含相同的列、表达式或聚集函数，不过各个列不需要以相同的次序列出
- 列数据类型必须兼容：类型不必完全相同，但必须是 DBMS 可以隐式转换的类型
- UNION 默认会从查询结果集中自动去除重复的行，如果不想去掉重复的行，则需要使用 UNION ALL 关键字
- 在用 UNION 组合查询时，如果要使用排序，只能使用一条 ORDER BY 子句，并且它必须出现在最后一条 SELECT 语句之后

# 全文索引

在使用全文本搜索时，MySQL 不需要分别查看每个行，不需要分别分析和处理每个词。MySQL 创建指定列中各词的一个索引，搜索可以针对这些词进行。这样，MySQL 可以快速有效地决定哪些词匹配（哪些行包含它们），哪些词不匹配，它们匹配的频率，等等。全文本搜索的一
个重要部分就是对结果排序，具有较高等级的行先返回，如要搜索的词在某行是第 4 个单词，在另外一行中是第 10 个单词，那么在第 4 个单词出现的行会优先被返回。

为了使用全文索引，必须对搜索的列创建全文索引，在创建索引之后，SELECT 可与 Match() 和 Against() 一起使用以实际执行搜索。

要创建全文索引，需要在创建表时进行声明，同时，被索引的列之间使用逗号隔开

```sql
CREATE TABLE productnotes
(
    note_id int NOT NULL AUTO INCREMENT,
    prod_id char(10) NOT NULL,
    note_date datetime NOT NULL,
    note_text text NULL,
    PRIMARY KEY(note_id),
    FULLTEXT(note_text)
)ENGINE=MyISAM;
```

> 不要在导入数据时使用 FULLTEXT 更新索引要花时间，虽然不是很多，但毕竟要花时间。如果正在导入数据到一个新表，此时不应该启用FULLTEXT 索引。应该首先导入所有数据，然后再修改表，定义FULLTEXT。这样有助于更快地导入数据（而且使索引数据的总时间小于在导入每行时分别进行索引所需的总时间）

## 使用全文索引

在索引之后，使用两个函数 Match() 和 Against() 执行全文本搜索，其中 Match() 指定被搜索的列，Against() 指定要使用的搜索表达式。

```sql
SELECt note_text
FROM productnotes
WHERE Match(note_text) Against('rabbit');
```

分析：

此 SELECT 语句检索单个列 note_text。由于 WHERE 子句，一个全文本搜索被执行。Match(note_text) 指示 MySQL 针对指定的列进行搜索，Against('rabbit') 指定词 rabbit 作为搜索文本

# 插入语句

MySQL 用单条 INSERT 语句处理多个插入比使用多条 INSERT 语句快。

## 插入检索出来的数据(INSERT SELECT)

```sql
INSERT INTO customers 
(
    cust_id,
    cust_contact,
    cust_email,
    cust_name,
    cust_address,
    cust_city,
    cust_state,
    cust_zip,
    cust_country
)
SELECT 
	cust_id,
	cust_contact,
	cust_email,
	cust_name,
	cust_address,
	cust_city,
	cust_state,
	cust_zip,
	cust_country
FROM custnew;
```

这个例子使用 INSERT SELECT 从 custnew 中将所有数据导入 customers

> INSERT SELECT 中的列名 为简单起见，这个例子在 INSERT 和 SELECT 语句中使用了相同的列名。但是，不一定要求列名匹配。事实上，**MySQL 甚至不关心 SELECT 返回的列名。它使用的是列的位置，因此 SELECT 中的第一列（不管其列名）将用来填充表列中指定的第一个列，第二列将用来填充表列中指定的第二个列，如此等等。**这对于从使用不同列名的表中导入数据是非常有用的。

INSERT SELECT 中 SELECT 语句可包含 WHERE 子句以过滤插入的数据。

# 创建表

## AUTO_INCREMENT

每个表只允许一个 AUTO_INCREMENT 列，而且它必须被索引

获取 AUTO_INCREMENT 自动增长的值：SELECT last_insert_id();

## 默认值 DEFAULT

MySQL 不允许使用函数作为默认值，它只支持常量。尽可能的使用默认值，而不要使用 NULL

# 视图

视图是虚拟的表,它不包含表中应该有的任何列或数据，它包含的是一个 SQL 查询。

视图仅仅是用来查看存储在别处数据的一种方式

## 视图常见的应用

- 重用SQL语句。
- 简化复杂的 SQL 操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节。
- 使用表的组成部分而不是整个表。
- 保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限。
- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。

在视图创建之后，可以用与表基本相同的方式利用它们。可以对视图执行 SELECT 操作，过滤和排序数据，将视图联结到其他视图或表，甚
至能添加和更新数据(添加和更新数据存在某些限制)

## 视图的规则和限制

- 与表一样，视图必须唯一命名（不能给视图取与别的视图或表相同的名字）
- 对于可以创建的视图数目没有限制。
- 为了创建视图，必须具有足够的访问权限。这些限制通常由数据库管理人员授予。
- 视图可以嵌套，即可以利用从其他视图中检索数据的查询来构造一个视图。
- ORDER BY 可以用在视图中，但如果从该视图检索数据 SELECT 中也含有 ORDER BY，那么该视图中的 ORDER BY 将被覆盖。
- 视图不能索引，也不能有关联的触发器或默认值。
- 视图可以和表一起使用。例如，编写一条联结表和视图的 SELECT 语句。

在 MySQL 处理在视图上的查询时，它会把在视图上执行的 SQL 语句中的 WHERE 子句添加到创建视图的 SQL 语句中的 WHERE 子句中，以便正确过滤数据。

## 视图的更新

如果视图定义中有以下操作，则不能进行视图的更新:

- 分组（使用GROUP BY和HAVING）
- 联结
- 子查询
- 并
- 聚集函数(Min()、Count()、Sum()等)
- DISTINCT
- 导出（计算）列

通常来说，视图是用于查询数据的，不用于更新数据

## 性能问题

因为视图不包含数据，所以每次使用视图时，都必须处理查询执行时所需的任一个检索。如果你用多个联结和过滤创建了复杂的视图或者嵌套了视图，可能会发现性能下降得很厉害。因此，在部署使用了大量视图的应用前，应该进行测试。

# 触发器

触发器是 MySQL 响应修改语句而自动执行的一条 MySQL 语句，修改语句包括：

- DELETE
- INSERT
- UPDATE

其他的语句不支持触发器, 同时只有表才支持触发器，视图不支持触发器

触发器按每个表每个事件每次地定义，每个表每个事件每次只允许一个触发器。因此，每个表最多支持 6 个触发器（每条 INSERT、UPDATE 和 DELETE 的之前和之后）。单一触发器不能与多个事件或多个表关联，所以，如果你需要一个对 INSERT 和 UPDATE 操作执行的触发器，则应该定义两个触发器。

## INSERT 触发器

INSERT 触发器在 INSERT 语句执行之前或之后执行:

- 在 INSERT 触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行；
- 在 BEFORE INSERT 触发器中，NEW 中的值也可以被更新(允许更改被插入的值)；
- 对于 AUTO_INCREMENT 列，NEW 在 INSERT 执行之前包含 0，在 INSERT 执行之后包含新的自动生成值。

## DELETE 触发器

DELETE 触发器在 DELETE 语句执行之前或之后执行:

- 在 DELETE 触发器代码内，你可以引用一个名为 OLD 的虚拟表，访问被删除的行；
- OLD 中的值全都是只读的，不能更新

## UPDATE 触发器

UPDATE 触发器在 UPDATE 语句执行之前或之后执行：

- 在 UPDATE 触发器代码中，你可以引用一个名为 OLD 的虚拟表访问以前（UPDATE 语句前）的值，引用一个名为 NEW 的虚拟表访问新更新的值；
- 在 BEFORE UPDATE 触发器中，NEW 中的值可能也被更新（允许更改将要用于 UPDATE 语句中的值）；
- OLD 中的值全都是只读的，不能更新。

MySQL 触发器中不支持 CALL 语句。这表示不能从触发器内调用存储过程。所需的存储过程代码需要复制到触发器内。

# 事务

事务处理是用来管理 INSERT、UPDATE 和 DELETE 语句的。你不能回退 SELECT 语句。你不能回退 CREATE 或 DROP 操作。事务处理块中可以使用这两条语句，但如果你执行回退，它们不会被撤销。