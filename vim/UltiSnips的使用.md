# UltiSnips 简述

UltiSnps 插件包括两部分，一个是渲染引擎，另一个是代码片段本身

```vimscript
# 渲染引擎
Plug 'SirVer/ultisnips'
# 代码片段仓库，可以是自己的
Plug 'honza/vim-snippets'
```

ultisnips 能够接受的 snippets 全部在 `vim-snippets/UltiSnps` 目录下，命名规则是：`<filetype>.snippet`

UltiSnips 需要 vim 支持 Python

## UltiSnips Snippets 格式

```text
snippet 触发字符 ["代码片段说明" [参数]]
  代码片段
endsnippet
```

其中 `[]` 中的内容为可选项

### 例子

```text
snippet if "if (condition) { ... }"
if(${1:true}){
  $0
}
endsnippet
```

当在代码中输入 if 时，会自动将 `snippet` 与 `endsnippet` 之间的代码插入到编辑器中

`${1:true}` 表示这是第一个触发点点，占位符为 `true`, 如果占位符没用，则可以直接使用 `$1, $2, $3, ...`

## 将 visual 模式中选择的代码作为占位符

`${VIUSAL}` 表示将 visual 模式中选择的代码来代替占位符：`${VISUAL}`

## 代码片段中的参数

* `b` 在每一行的开始触发
* `i` 表示可以在单词中间触发
* `w` 表示在每一个新单词的开始触发
* `r` 表示触发的字符可以是一个正则表达式
* `t` 表示展开的代码片段中如果有制表符，原样输出，即使在 vim 中设置了 expandtab
* `m` 表示删除代码片段右边的所有空白字符
* `e` 表示自定义上下文
* `A` 表示自动触发，即只要满足条件就自动触发

## 模板渲染引擎

Ultisnips 定义的 snippets 支持三种不同语言的注入：shell, vimscript, python，在代码片段中用反引号进行包裹

* shell: 直接将 shell 命令放入反引号中
* vimscript: 在反引号中使用 `!v` 开头，表示这是一个 vimscript 脚本
* python: 在反引号中使用：`!p` 开头，表示这是一个 python 脚本

例如：

```text
snippet today
Today is the `date`.
endsnippet

snippet indent
Indent is: `!v indent(".")`.
endsnippet

snippet tt
${1:Hello} - ${2:World}

`!p
snip.rv="""
{0}
{2}
{2}
{3}
""".format(
    fn,
    snip.ft,
    snip.basename,
    t[1] + '-' t[2],
    snip.v.text
)
`
endsnippet
```

### Python 代码说明

在 snippets 中执行 python 代码是 Ultisnips 最强大的功能，以前缀 `!p` 开始。Ultisnips 会向 python 中注入一些变量，可以在 python 代码直接对其进行操作

* `fn` - 表示当前文件名
* `path` - 当前文件名的路径
* `t` - 占位符的字典，可以使用 `t[1]`, `t[2]`, `t.v` 来取占位符内容
* `snip` - `UltiSnips.TextObjects.SnippetUtil` 对象的一个实例
* `match` - 正则代码片段时返回的匹配元素（非常强大）

其中最常用的 snip 对象提供了下面一些变量：

* `snip.rv` 表示 return value，python 代码执行后处理过的字符串赋给 rv 即可
* `snip.fn` 表示当前文件名
* `snip.ft` 表示当前文件类型
* `snip.v` 表示 VISUAL 模式变量，其中 snip.v.mode 表示模式类型，snip.v.text 表示 VISUAL 模式中选择的字符

## 自定义上下文

自定义上下文可以通过正则匹配来决定代码片断是否可用，比如判断在指定的 if 语句里面才起作用的代码片断，定义格式如下：

> snippet 触发字符 “描述” “表达式” 参数

比如我们定义一个 只有 在上一行以 `if (DEVELOPMENT) {` 开头才可以展开的代码片段

```text
snippet dbg "if (DEVELOPMENT) dbg" "re.match('^if \(DEVELOPMENT\) \{', snip.buffer[snip.line-1])" be
debugger;
endsnippet
```

## 行内连续展开

这个常见于需要连续展开代码片段的情况，比如，有两个片段，一个打印变量，一个处理 JSON 序列化。这时需要使用参数选项 i

## 使用正则表达式的代码片段

通常写代码的时候需要使用 log, print 等来打印上下文中的变量。使用普通片段按 cl 展示 console.log() 然后把变量字符复制进括号，这样操作会比较复杂。使用正则来动态匹配前面的字符可以很好的解决这个问题

```text
# 展开 console.log
snippet "([^\s]\w+)\.log" "console.log(postfix)" r
console.log(`!p snip.rv = match.group(1)`)$0
endsnippet
# 当前行转换成大写
snippet "([^\s].*)\.upper" "Uppercase(postfix)" r
`!p snip.rv = match.group(1).upper()`$0
endsnippet
# 上一个单词转换成小写
snippet "([^\s]\w+)\.lower" "Lowercase(postfix)" r
`!p snip.rv = match.group(1).lower()`$0
endsnippet
```

__注意__：正则代码片段只适用于单行文本处理，如果是多行转换还是得用到下面的 python + VISUAL 代码片段来处理

## 使用 python 解释器 +  VISUAL 实现代码注释功能

通常我们需要使用一大堆插件来实现各种代码的注释功能。不过 Ultisnips 提供了 VISUAL 模式可以提取 vim 可视模式中选择的内容到代码片段里面，于是我们就可以结合起来制作一个**具有注释功能的代码片段**

流程大概是这样的：

1. 进入 vim 可视模式，选择要注释的内容
2. 按 tab，清除选择内容
3. 输入代码片段触发字符，按 tab 完成

由于实现的 python 代码相对复杂一些，主要分成两个方法。单行注释和多行注释，注意 Ultisnips 中可以直接写 python 但是大段的方法建议放在插件目录下面的 pythonx 目录下面，使用的时候在对应的代码片段中的全局 python 代码 `global !p` 引入即可

**单行注释**(pythonx/javascript_snippets.py)：

```python
def comment(snip, START="", END=""):
    lines = snip.v.text.split('\n')[:-1]
    first_line = lines[0]
    spaces = ''
    initial_indent = snip._initial_indent

    # Get the first non-empty line
    for idx, l in enumerate(lines):
        if l.strip() != '':
            first_line = lines[idx]
            sp = re.findall(r'^\s+', first_line)
            if len(sp):
                spaces = sp[0]
            break            

    # Uncomment
    if first_line.strip().startswith(START):
        result = [line.replace(START, "", 1).replace(END, "", 1) if line.strip() else line for line in lines]
    else:
        result = [f'{spaces}{START}{line[len(spaces):]}{END}' if line.strip() else line for line in lines ]

    # Remove initial indent
    if result[0] and initial_indent:
        result[0] = result[0].replace(initial_indent, '', 1)

    if result:
        return '\n'.join(result)
    else:
        return ''
```

**多行注释：**

```python
def comment_inline(snip, START="/* ", END=" */"):
    text = snip.v.text
    lines = text.split('\n')[:-1]
    first_line = lines[0]
    initial_indent = snip._initial_indent
    spaces = ''

    # Get the first non-empty line
    for idx, l in enumerate(lines):
        if l.strip() != '':
            first_line = lines[idx]
            sp = re.findall(r'^\s+', first_line)
            if len(sp):
                spaces = sp[0]
            break            

    if text.strip().startswith(START):
        result = text.replace(START, '', 1).replace(END, '', 1)
    else:
        result = text.replace(spaces, spaces + START, 1).rstrip('\n') + END + '\n'

    if initial_indent:
        result = result.replace(initial_indent, '', 1)

    return result
```

**代码片段定义：**

```text
global !p
from javascript_snippets import (
	comment, comment_inline
)
endglobal

# ...

snippet c "Toggle comment every single line"
`!p
snip.rv = comment(snip, START='// ', END='')
`$0
endsnippet

snippet ci "Toggle comment inline."
`!p
snip.rv = comment_inline(snip, START="/* ", END=" */")
`$0
endsnippet
```
