# VimScript语法

## 映射

```vimscirpt
noremap <expr>s test()
```

> <expr> 表示将 s 映射为后面函数的执行结果

## echo 和 echom

这两个命令都是用来打印信息，但是 echom 命令输出的东西可以通过 messages 命令进行查看，echo  命令输出的东西则不能够通过该命令进行查看

## 自动命令 autocmd

autocmd 命令可以让 vim 在特定的场合自动执行指定的命令

### 命令格式

```vimscript
autocmd <event> <pattern> <command>
```

参数说明：

- event ==> 在什么事件下触发自动命令。可以同时指定多个事件，用 `,` 进行隔开。可以使用 `:help autocmd-events` 命令查看所有的事件
- pattern ==> 触发 event 事件后，再次设置条件进行过滤
- command ==> 符合条件后执行的命令

示例：

```vimscript
autocmd BufNewFile *.txt :writen
```

上述命令表示使用 vim 打开一个不存在的 txt 文件时，vim会自动在磁盘中创建一个对应的文件。vim 在打开一个不存在的文件时，如果不保存，是不会在磁盘中创建的对应的文件的

### 常用的event

| event                 | 作用                                 |
| --------------------- | ------------------------------------ |
| BufNew                | 创建一个新的 buffer 时触发该事件     |
| BufNewFile            | 在打开一个不存在的文件时触发该事件   |
| BufWritePre           | 在保存文件之前触发该事件             |
| BufReadPre            | 在打开一个已存在的文件之前触发该事件 |
| BufReadPost / BufRead | 打开一个已存在的文件之后触发该事件   |
|                       |                                      |
| FileType              | 根据指定的文件类型触发对应的事件     |
|                       |                                      |
|                       |                                      |

### 自动命令组

加载`~/.vimrc`配置文件的时候，Vim 会重新读取整个文件，包括你所定义的任何自动命令！这就意味着每次你加载你的`~/.vimrc`文件的时候，Vim 都会复制之前的自动命令，这会降低 Vim 的运行速度，因为它会一次又一次地执行相同的命令。

对于这个问题，Vim有一个解决方案。这个解决方案的第一步是将相关的自动命令收集起来放到一个已命名的组（groups）中。

新开一个 Vim 实例，这样可以清除之前所创建的自动命令。然后运行下面的命令：

```vimscript
augroup testgroup
    autocmd BufWrite * :echom "Foo"
    autocmd BufWrite * :echom "Bar"
augroup END
```

## execute 命令

execute 命令后面跟一个字符串，该字符串被当作 vim 命令执行, 如:

```vimscript
execute "echom \"Hello World\""
```

在 execute 命令中，可以识别特殊字符，普通命令则不可以，这是 execute 命令比较特别的地方，而且 execute 命令也比较直观

execute 命令允许使用任意的字符来创建命令

## 变量

在 vim 中使用 let 命令声明一个变量

```vimscript
let name = "AutMaple"
echo name

let num = 10
let num2 = 20
let num = num + num2
echo num
```

### 操作内置选项

在输出 vim 的内置选项时，可以用如下方式进行输出

```vimscript
set testwidth=80
echo &textwidth
let &textwidth = &textwidth + 10
echo &textwidth
```

注意：` echo &name` 这个写法是错误的，该语法只能输出内置选项，对于自定义变量不能够使用该语法进行输出

### 操作寄存器

操作寄存器时，直接使用 `@<寄存器编号>`就可以操作对应编号的寄存器

```vimscript
" 设置寄存器 a 的内容为 Hello AutMaple
let @a = "Hello AutMaple"
```

搜索模式的模式字符串被存储在 `/` 寄存器中，可以使用 `@/` 方式读取该寄存器中的内容

### 变量作用域

但使用 `b:`修饰一个变量时，表示该变量只在当前 buffer 中有效

```vimscript
let b:num = 666
```

## 流程控制

### 条件语句

vim 中使用 `if` `elseif` `else` `endif` 来表示条件语句。其中 `0` 表示 `false` , `非0` 表示为 `true`

#### 强制类型转换

- 以一个数字开头的字符串会被强制转换成数字，否则会转换成`0`。 如： `“20hello”` 被转换成 `20`， `“hello”`  被转换成 `0`

- 数字和字符串进行运算时，会将字符串转换成数字之后进行运算。如：`"20hello" + 10` 最终输出是 `30`

- 在所有的强制转换完成*后*，当`if`的判断条件等于非零整数时，Vim会执行`if`语句体

### 比较运算符

`>` `<` `==`

使用 `==`运算符时，需要小心，因为在使用 `==` 运算符进行字符串的比较时，是否对大小写敏感取决于用户的配置，如果设置了 `set igorecase`则会忽略大小写，如果设置了 `set noigorecase` 则会匹配大小写

```vimscript
set ignorecase
if "Hello" == "hello"
    echom "ignorecase"
else
    echom "noignorecase"
endif


set noignorecase
if "Hello" == "hello"
    echom "ignorecase"
else
    echom "noignorecase"
endif
```

接下来的两个运算符是不需要管用户的设置的

- `==?` 表示大小写不敏感的比较

- `==#`表示大小写敏感的比较

### for 循环

vim 中使用 for 和 endfor 表示一个循环，语法

```vimscript
for ... in ...
	...
	...
endfor
```

### while 循环

vim 中使用 while 和 endwhile 来表示一个while 循环，语法

```vimscirpt
while <condition>
	....
	....
endwhile
```

## 函数

vim 中使用 `function` 和 `endfunction` 声明一个函数,

使用一个函数可以使用 `call 函数名()`

使用 `return `关键字返回数据, 如果不显示返回一个数据，默认返回数字 `0`

**注意**：没有作用域限制的 Vimscript 函数必须以一个大写字母开头！

当一个函数内部要使用传递的参数时，需要使用前缀 `a:`修饰参数，不然会报错说找不到对应的参数

```vimscript
function Hello(name)
    echom "hello "
    echom a:name
endfunction

call Hello("AutMaple")
```

可变参数：`func(...)`

- `a:0` 表示可变参数的数量
- `a:n` 表示传递的第 n 个参数， n = 1, 2, 3, ...

```vimscript
function Hello(name, ...)
    echo a:name
    echo a:0
    echo a:1
    echo a:2
endfunction

call Hello("AutMaple", 1,2,3)
```

输出：

```vimscript
AutMaple
3
1
2
```

## 数字

在 vimscript 中的 浮点数 float， 如果使用科学计数法，必须有小数点和小数点后面的数字

 在进行运算时，运算规则和 C 语言中的运算规则相同

## 字符串

vim中的字符串可以使用单引号表示，也可以使用双引号表示，单引号表示的字面量所见即所得，不需要进行转义，而双引号表示的字符串，特殊字符需要进行转义

### 字符串的拼接

字符串的连接使用 `.` 运算符而不是 `+` 运算符，使用 `+`  运算符会将字符串转换成数字后进行数字运算

其中数字遇上 `.` 运算符也会被转换成字符串之后进行拼接, 其中需要注意的一点就是当浮点数和字符串进行拼接的时候会报错

### 字符串函数

| 函数                        | 作用                                                         |
| --------------------------- | ------------------------------------------------------------ |
| strlen(str)                 | 返回字符串的长度                                             |
| len(str)                    | 返回字符串的长度                                             |
| split(str, flag)            | 按照 flag 分割符号进行分割, 如果没有指定 flag 默认使用空格进行分割 |
| join([str1,str2...],  flag) | 使用 flag 连接字符串数组中的字符串                           |
| tolower(str)                | 转化成小写                                                   |
| toupper(str)                | 转换成大写                                                   |

## 执行不同模式的命令

由于不同的用户的映射不同，因此可以在对应的模式后面加上 `!`的方式来忽略用户的映射而执行最原始的行为，例如： `normal! G` ==> 表示将光标移动到文件最后一行

## 列表 list

可以进行的操作：切割，拼接,这些操作和 python 的类似

### 内置函数

| 函数                      | 作用                                                         |
| ------------------------- | ------------------------------------------------------------ |
| add(list, elem)           | 添加一个元素到 list 中                                       |
| len(lsit)                 | 返回 list 的长度                                             |
| get(list, index, default) | 返回 list 对于 index 位置的值，如果没有对应的 index 则返回默认值 |
| index(list, str)          | 返回 list 中第一次出现 str 的索引                            |
| reverse(list)             | 将一个 list 进行翻转                                         |
|                           |                                                              |
|                           |                                                              |

```vimscript
let list = [1,3,3]
call add(list, 1)
echo list

let list = [1,3,3]
call reverse(list)
echo list
```

## 字典

vim 中的字典 key 会被强制转换成 字符串

vim 字典的访问可以使用 `dictname[“key”]` 或者 `dictname.key`

移除字典中的值可以使用 `remove(dicname, key)`和 `unlet dictname.key`这两种方式，第一种方式会返回对应 `key` 的值，第二种方式不会返回

### 函数

| 函数                        | 作用                                                        |
| --------------------------- | ----------------------------------------------------------- |
| get(dictname, key, default) | 获取指定的 key 对应的值，如果值找不到对应的 key 返回默认值  |
| has_key(dictname, key)      | 判断字典中是否有指定的 key, 1 为真，0为假                   |
| items(dictname)             | 返回对应的键值对 list ==> [[key1, val1], [key2, val2].....] |
| keys(dictname)              | 返回对应的 key 值 list                                      |
| values(dictname)            | 返回对应的 value 值 list                                    |

```vimscript
let dict = {"a": 1, "b": 2}

let items = items(dict)
echo items

let keys = keys(dict)
echo keys

let values = values(dict)
echo values
```

## 常用的内置函数

| 函数                   | 作用                   |
| ---------------------- | ---------------------- |
| `getline()`            | 返回当前行的字符 list  |
| `setreg(reg, content)` | 设置指定寄存器中的内容 |
| `getreg(regName)`      | 取出指定寄存器中的内容 |
|                        |                        |
|                        |                        |
|                        |                        |
|                        |                        |

