# Mysql 使用

## Mysql 聚合函数

| 函数                 | 描述             |
| -------------------- | ---------------- |
| AVG([DISTINCT] expr) | 求数值列的平均值 |
| COUNT()              | 统计行数         |
| MAX([DISTINCT] expr) | 统计最大值       |
| MIN([DISTINCT] expr) | 统计最小值       |
| SUM([DISTINCT] expr) | 对某一列进行求和 |

1. 每个函数接收一个参数
2. 默认情况下，函数会忽略值为 null 的行, 不参与计算
3. 可以使用 DISTINCT 关键字去除重复的列值，相同的列值只计算一次
4. 当使用组函数的 select 语句中没有 group by 子句时，中间结果集中的所有行自动形成一组，然后计算组函数
5. 组函数不允许嵌套，例如：`count(max(…))`
6. 组函数的参数可以是列或是函数表达式
7. 一个 SELECT 子句中可出现多个聚集函数

## HAVING

在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与聚合函数一起使用。

当同时含有 where 子句、group by 子句 、having 子句及聚合函数时，执行顺序如下：

1. 执行 where 子句查找符合条件的数据；
2. 使用 group by 子句对数据进行分组；
3. 对 group by 子句形成的组运行聚集函数计算每一组的值；
4. 用 having 子句去掉不符合条件的组。xiangideaifsd fsd xiangideaifsd fsd idaidea

## Msyql 的逻辑

Mysql 使用三值逻辑：true，fasle， unknown。任何与 `NULL` 值进行的比较都会与 UNKNOWN 做比较, 包括 null 和 null 进行比较。这就是为什么 MySQL 提供 `IS NULL` 和 `IS NOT NULL` 两种操作来对 `NULL` 特殊判断。

查询结果只会包含 WHERE 子句里的判断结果为 true 的行！不包含判断结果为 false 和 unknown 的行, 对 null 进行比较，返回都是 unknown

### 三值逻辑运算

unknown 小写，是第三个真值。与作为 NULL 的一种 UNKNOWN (未知)是不同的东西。小写是明确的布尔类型的真值，后者大写的既不是值也不是变量。为了对比不同：看 x=x 的情况。

```sql
unknown = unknown -> true
UNKNOWN = UNKNOWN -> unknown
```


重点：【三值逻辑运算】

```sql
NOT unknown => unknown

true    OR unknown => true
unknown OR unknown => unknown
false   OR unknown => unknown

true    AND unknown => unknown
unknown AND unknown => unknown
false   AND unknown => false
```

记忆：优先级：

```sql
AND: false > unknown > true
OR:  true > unknown > false
```

## union

UNION 操作符合并两个或多个 SELECT 语句的结果。

UNION 内部的每个 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每个 SELECT 语句中的列的顺序必须相同。

默认地，UNION 操作符选取不同的值。如果允许重复的值，请使用 UNION ALL。

UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名。

## IF 表达式

```sql
IF(expr1, expr2, expr3)
```

如果 expr1 表达式为 true，返回 expr2

如果 expr1 表达式为 false，返回 expr3

## IFNULL 表达式

```sql
IFNULL(expr1, expr2)
```

如果 expr1 不为 null， 直接返回 expr1

如果 expr1 为 null，返回 expr2

```sql
select ifnull(null, 1), ifnull(2, 1);
+----------------+--------------+
| ifnull(null,1) | ifnull(2, 1) |
+----------------+--------------+
|              1 |            2 |
+----------------+--------------+
1 row in set (0.01 sec)

```

## NULLIF 表达式

```sql
NULLIF(expr1, expr2)
```

如果 expr1 == expr2，则返回 null

如果 expr1 != epxr2，则返回 expr1

```sql
select nullif(1,1), nullif(123,12);
+-------------+----------------+
| nullif(1,1) | nullif(123,12) |
+-------------+----------------+
|        NULL |            123 |
+-------------+----------------+
1 row in set (0.00 sec)
```

## if-then-else 表达式

```sql
case [<col_name>]
	when <compare_value> then <result>
	[when <compare_value> thne <result>]
	....
	[else <result>] end [new_name]
```

可以当作 switch，这时需要给出 col_name

如果不想当作 switch，可以直接写 when，不写 col_name,此时跟 if-then-else 语句相似

```sql
select user_id, case salary
	-> when 1000 then 'low'
	-> when 2000 then 'mid'
	-> when 3000 then 'high'
	-> else '无效值' end salary_grade
	-> from salary_tab;
+---------+--------------+
| user_id | saalry_grade |
+---------+--------------+
|        1|			  low|
|        2|			  mid|
|        3|			 high|
|        4|		    无效值|
|        5|			  low|
+---------+--------------+
```

## REGEXP 正则表达式

```sql
where <col> regexp <expr>
```

或者

```sql
where <col> rlike <expr>
```

会将匹配 expr 的行放入结果集中

## 查询时设置默认值

```sql
select 'm' sex from <table_name>
```

- sex 这一列的值默认为 `m`

## count()

count() 函数计数的原理是传入的值是 true 还是 null，如果是 null 就不计数，否则就计数，true 的定义是除了 null 为 false， 其他的都是 true，常数 0 也被认为是 true;

```sql
select count(0) from activity;
+----------+
| count(0) |
+----------+
|        5 |
+----------+
```

### count(column_name)

返回指定列中非 null 值的数量

### count(distinct column_name)

返回指定列不同值的数目,不包括值为 null 的行

### count(*)

返回表的记录数

> COUNT(常量) 和 COUNT(*)表示的是直接查询符合条件的数据库表的行数。
>
> COUNT(列名) 表示的是查询符合条件的列的值不为 NULL 的行数。

`count(1)`, `count(*)`

## sum()

SUM() 函数返回数值列所有数字的和

## count 和 sum 函数中使用 if 表达式

在 sum 中使用 if 表达式

```sql
sum(if(expr1, expr2,expr3))
```

如果 expr1 为 true，则 sum += expr2

如果 expr2 为 false，则 sum += expr3

在 count 中使用 if 表达式

```sql
count(if(expr1, true, null))
```

如果 count 要配合 if 表达式使用，必须使用上述表达式

## datediff()

datediff() 函数可以返回两个日期时间的差值

```sql
# datepart 默认是 day， 可以省略，还可以是年、月，时，分、秒等
DATEDIFF(datepart,startdate,enddate) # startdate - enddate
```

```text
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
```

```sql
select
    w1.id as Id
from
    Weather w1,Weather w2
where
    datediff(w1.recordDate, w2.recordDate) = 1 and w1.temperature > w2.temperature;
```

## min() 和 max()

min(): 返回一列中的最小值

max(): 返回一列中的最大值

```text
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
```

```sql
select
	player_id, min(event_date) as first_login
from 
	Activity
group by 
	player_id;
```

## mod()

```sql
mod(num, base) # num % base
```

## coalesce()

```sql
coalesce(expr1, expr2, .... , exprn)
```

依次比较计算各表达式，遇到非 null 值是直接返回那个非 null 值

```sql
coalesce(null, null, null, 3,4,5) # 返回 3
```

## group_concat()

用于将某一列的值拼接成一个字符串

```sql
group_concat(distinct <expr1>
            [order by <expr2>]
            [separator <sep>]);
```

- 默认按照升序进行排序
- 默认的连接符是 `,` 逗号

