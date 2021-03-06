# lua 基本语法

默认情况下，lua 中的变量都是全局变量, 要删除一个全局变量，只需要将其赋值为 nil 即可

## 数据类型

| 数据类型 | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| nil      | 只有值nil属于该类，表示一个无效值（在条件表达式中相当于false）。 |
| boolean  | 包含两个值：false和true。                                    |
| number   | 表示双精度类型的实浮点数, lua 中，只有这一种数字类型         |
| string   | 字符串由一对双引号或单引号来表示,也可以用 `[[]]` 来表示一块（可换行）字符串 |
| function | 由 C 或 Lua 编写的函数                                       |
| userdata | 表示任意存储在变量中的C数据结构                              |
| thread   | 线程， 表示执行的独立线路，用于执行协同程序                  |
| table    | Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字、字符串或表类型。在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。 **相当于字典** |

- 可以使用 `type()` 函数来测试数据类型, 其返回值是**字符串**，这需要注意

### 字符串

- 当字符串进行算数运算(+ - * /)的时候，默认将字符串转换成数字进行运算, 如果无法转换，则会报错
- 字符串的连接使用 `..`

- 使用 `#` 计算字符串的长度

### table 表

- Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字或者是字符串
- 数组的索引从 **1** 开始， 这和其他的语言不一样
- table 不会固定长度大小，有新数据添加时 table 长度会自动增长，没初始的 table 都是 nil。

## 变量

- Lua 变量有三种类型：全局变量、局部变量、表中的域

- Lua 中的变量全是全局变量，哪怕是语句块或是函数里，除非用 local 显式声明为局部变量

- 局部变量的作用域为从声明位置开始到所在语句块结束

- 变量的默认值均为 nil

- Lua 可以对多个变量同时赋值，变量列表和值列表的各个元素用逗号分开，赋值语句右边的值会依次赋给左边的变量

- 当变量个数和值的个数不一致时，Lua会一直以变量个数为基础采取以下策略：

  ```text
  a. 变量个数 > 值的个数             按变量个数补足nil
  b. 变量个数 < 值的个数             多余的值会被忽略
  ```

## 循环

| 循环类型                                                     | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [while 循环](https://www.runoob.com/lua/lua-while-loop.html) | 在条件为 true 时，让程序重复地执行某些语句。执行语句前会先检查条件是否为 true。 |
| [for 循环](https://www.runoob.com/lua/lua-for-loop.html)     | 重复执行指定语句，重复次数可在 for 语句中控制。              |
| [repeat...until](https://www.runoob.com/lua/lua-repeat-until-loop.html) | 重复执行循环，直到 指定的条件为真时为止                      |

## 流程控制

| 语句                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [if 语句](https://www.runoob.com/lua/if-statement-in-lua.html) | **if 语句** 由一个布尔表达式作为条件判断，其后紧跟其他语句组成。 |
| [if...else 语句](https://www.runoob.com/lua/if-else-statement-in-lua.html) | **if 语句** 可以与 **else 语句**搭配使用, 在 if 条件表达式为 false 时执行 else 语句代码。 |
| [if 嵌套语句](https://www.runoob.com/lua/nested-if-statements-in-lua.html) | 你可以在**if** 或 **else if**中使用一个或多个 **if** 或 **else if** 语句 |

## 函数

```lua
[scope] function funcName([arg1,arg2,arg3,...])
    ...
    return ...
   end
```

- scope => 指定函数的作用范围，默认是全局范围，如果想定义一个局部函数，可以使用 local 关键字

- return => lua 中的函数可以返回多个值

- 在 lua 中，函数也可以作为参数进行传递
- 可变参数：在 lua 中使用 `...` 表示可变参数

- 通过 `select("#",...)` 获取可变参数的数量
- **select(n, …)** 用于返回从第 n 个位置开始到结束位置的所有参数列表。

## 算数运算符

| 操作符 | 描述 | 实例               |
| :----- | :--- | :----------------- |
| +      | 加法 | A + B 输出结果 30  |
| -      | 减法 | A - B 输出结果 -10 |
| *      | 乘法 | A * B 输出结果 200 |
| /      | 除法 | B / A 输出结果 2   |
| %      | 取余 | B % A 输出结果 0   |
| ^      | 乘幂 | A^2 输出结果 100   |
| -      | 负号 | -A 输出结果 -10    |

## 关系运算符

下表列出了 Lua 语言中的常用关系运算符，设定 A 的值为10，B 的值为 20：

| 操作符 | 描述                                                         | 实例                  |
| :----- | :----------------------------------------------------------- | :-------------------- |
| ==     | 等于，检测两个值是否相等，相等返回 true，否则返回 false      | (A == B) 为 false。   |
| ~=     | 不等于，检测两个值是否相等，不相等返回 true，否则返回 false  | (A ~= B) 为 true。    |
| >      | 大于，如果左边的值大于右边的值，返回 true，否则返回 false    | (A > B) 为 false。    |
| <      | 小于，如果左边的值大于右边的值，返回 false，否则返回 true    | (A < B) 为 true。     |
| >=     | 大于等于，如果左边的值大于等于右边的值，返回 true，否则返回 false | (A >= B) 返回 false。 |
| <=     | 小于等于， 如果左边的值小于等于右边的值，返回 true，否则返回 false | (A <= B) 返回 true。  |

## 逻辑运算符

下表列出了 Lua 语言中的常用逻辑运算符，设定 A 的值为 true，B 的值为 false：

| 操作符 | 描述                                                         | 实例                   |
| :----- | :----------------------------------------------------------- | :--------------------- |
| and    | 逻辑与操作符。 若 A 为 false，则返回 A，否则返回 B。         | (A and B) 为 false。   |
| or     | 逻辑或操作符。 若 A 为 true，则返回 A，否则返回 B。          | (A or B) 为 true。     |
| not    | 逻辑非操作符。与逻辑运算结果相反，如果条件为 true，逻辑非为 false。 | not(A and B) 为 true。 |

## 其他运算符

下表列出了 Lua 语言中的连接运算符与计算表或字符串长度的运算符：

| 操作符 | 描述                               | 实例                                                         |
| :----- | :--------------------------------- | :----------------------------------------------------------- |
| ..     | 连接两个字符串                     | a..b ，其中 a 为 "Hello " ， b 为 "World", 输出结果为 "Hello World"。 |
| #      | 一元运算符，返回字符串或表的长度。 | #"Hello" 返回 5                                              |

## 字符串

| 方法                                        | 描述                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| string.upper(argument)                      | 字符串全部转为大写字母                                       |
| string.lower(argument)                      | 字符串全部转为小写字母                                       |
| string.gsub(mainStr,oldStr,newStr[, count]) | 字符串替换                                                   |
| string.reverse(arg)                         | 字符串反转                                                   |
| string.format(...)                          | 格式化字符串                                                 |
| string.char(arg)                            | 将数字转换成字符                                             |
| string.byte(arg[, index])                   | 将字符转换成数字, 默认转换第一个字符                         |
| string.len(str)                             | 返回字符串的长度                                             |
| string.rep(str, count)                      | 返回 str 的 count 次拷贝                                     |
| string.gmatch(str, pattern)                 | 返回一个迭代器函数，每一次调用这个函数，返回一个在字符串 str 找到的下一个符合 pattern 描述的子串。如果参数 pattern 描述的字符串没有找到，迭代函数返回nil。 |
| string.match(str, pattern, start)           | 只寻找源字串 str 中的第一个配对. 参数 start 可选, 指定搜寻过程的起点, 默认为 1。在成功配对时, 函数将返回配对表达式中的所有捕获结果; 如果没有设置捕获标记, 则返回整个配对字符串. 当没有成功的配对时, 返回nil。 |
| string.sub(str,start[, end])                | 截取字符串, end 的值默认为 -1                                |
| string.find(str, pattern)                   | 查找字符串，并返回其下表                                     |

### 字符串 pattern 支持的模式

单个字符(除 **^$()%.[]\*+-?** 外): 与该字符自身配对

- .(点): 与任何字符配对

- %a: 与任何字母配对

- %c: 与任何控制符配对(例如\n)

- %d: 与任何数字配对

- %l: 与任何小写字母配对

- %p: 与任何标点(punctuation)配对

- %s: 与空白字符配对

- %u: 与任何大写字母配对

- %w: 与任何字母/数字配对

- %x: 与任何十六进制数配对

- %z: 与任何代表0的字符配对

- %x(此处x是非字母非数字字符): 与字符x配对. 主要用来处理表达式中有功能的字符(^$()%.[]*+-?)的配对问题, 例如%%与%配对

- [数个字符类]: 与任何[]中包含的字符类配对. 例如[%w_]与任何字母/数字, 或下划线符号(_)配对

- [^数个字符类]: 与任何不包含在[]中的字符类配对. 例如 `[^%s]` 与任何非空白字符配对

在模式匹配中有一些特殊字符，他们有特殊的意义，Lua中的特殊字符如下：

```
( ) . % + - * ? [ ^ $
```

`%` 用作特殊字符的转义字符，因此 `%.` 匹配点；`%%` 匹配字符 `%`。转义字符 `%` 不仅可以用来转义特殊字符，还可以用于所有的非字母的字符。

**模式条目可以是：**

- 单个字符类匹配该类别中任意单个字符；
- 单个字符类跟一个 '`*`'， 将匹配零或多个该类的字符。 这个条目总是匹配尽可能长的串；
- 单个字符类跟一个 '`+`'， 将匹配一或更多个该类的字符。 这个条目总是匹配尽可能长的串；
- 单个字符类跟一个 '`-`'， 将匹配零或更多个该类的字符。 和 '`*`' 不同， 这个条目总是匹配尽可能短的串；
- 单个字符类跟一个 '`?`'， 将匹配零或一个该类的字符。 只要有可能，它会匹配一个；
- `%*n*`， 这里的 *n* 可以从 1 到 9； 这个条目匹配一个等于 *n* 号捕获物（后面有描述）的子串。
- `%b*xy*`， 这里的 *x* 和 *y* 是两个明确的字符； 这个条目匹配以 *x* 开始 *y* 结束， 且其中 *x* 和 *y* 保持 *平衡* 的字符串。 意思是，如果从左到右读这个字符串，对每次读到一个 *x* 就 *+1* ，读到一个 *y* 就 *-1*， 最终结束处的那个 *y* 是第一个记数到 0 的 *y*。 举个例子，条目 `%b()` 可以匹配到括号平衡的表达式。
- `%f[*set*]`， 指 *边境模式*； 这个条目会匹配到一个位于 *set* 内某个字符之前的一个空串， 且这个位置的前一个字符不属于 *set* 。 集合 *set* 的含义如前面所述。 匹配出的那个空串之开始和结束点的计算就看成该处有个字符 '`\0`' 一样。

## 迭代器

语法格式

```lua
for k, v in [i]pairs(t) do
    print(k, v)
end
```

```lua
arr = {"google", "AutMaple"}

for key, value in pairs(arr) do 
    print(key, value)
end
```

### 无状态迭代器

无状态的迭代器是指不保留任何状态的迭代器，因此在循环中我们可以利用无状态迭代器避免创建闭包花费额外的代价每一次迭代，迭代函数都是用两个变量（状态常量和控制变量）的值作为参数被调用，一个无状态的迭代器只利用这两个值可以获取下一个元素

这种无状态迭代器的典型的简单的例子是 `ipairs`，它遍历数组的每一个元素，元素的索引需要是数值。

### 多状态的迭代器

很多情况下，迭代器需要保存多个状态信息而不是简单的状态常量和控制变量，最简单的方法是使用闭包，还有一种方法就是将所有的状态信息封装到 table 内，将 table 作为迭代器的状态常量，因为这种情况下可以将所有的信息存放在 table 内，所以迭代函数通常不需要第二个参数

## table 表

| 方法                                           | 描述                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| table.concat (table [, sep [, start [, end]]]) | 返回用 seq 分隔符拼接数组 table 从 start 位置到 end 位置的所有元素的字符串 |
| table.insert(table, [pos], value)              | 在 table 数组的 pos 位置插入元素 value，默认从表末插入       |
| table.remove(table[, pos])                     | 移除 pos 位置处的元素，其后元素前移，默认删除最后一个元素    |
| table.sort (table [, comp])                    | 对给定的 table 进行升序排序。                                |

## 模块与包

- Lua 的模块是由变量、函数等已知元素组成的 table。 因此创建一个模块很简单，就是创建一个 table，然后把需要导出的常量、函数放入其中，最后返回这个 table 就行
- 被 local 修饰的局部变量不能够被外部所使用
- 加载一个模块，只需要 `require("<moduleName>")` 就行, 引入的变量就是 `moduleName.lua` 文件返回的变量

