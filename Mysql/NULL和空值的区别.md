# NULL 和空值

所谓的空值即空字符串 `''`

## 各自占用的空间

```sql
select length(NULL), length(''), length('1');
```

输出：

```text
+------------+----------+-----------+
|length(null)|length('')|length('1')|
+------------+----------+-----------+
|NULL        |0         |1          |
+------------+----------+-----------+
```

官方对 NULL 的描述是：

> NULL columns require additional space in the row to record whether their values are NULL.

即 NULL 列需要行中额外的空间来记录该列的值是否为 NULL

## count() 函数对 NULL 和空值的处理

count(colName) 不会计算值为 NULL 的行，但是会计算值为空值的行