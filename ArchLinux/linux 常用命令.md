# 输出重定向

在 Shell 中，默认情况下，总有三个文件处于打开状态：标准输入(键盘输入)、标准输出(输出到屏幕)、标准错误(输出到屏幕)。它们对应的文件描述符分别是：0，1，2

- `>` 默认为标准输出重定向，与 `1>` 相同, 通常在写命令时会将 1 给省略掉
- `2>&1` 意思是把标准错误输出重定向到标准输出
- `&>file` 意思是把标准输出和标准错误输出都重定向到 file 文件中
- `&` 表示标注输出和标准错误输出

`/dev/null` 文件是一个黑洞，只进不出，即重定向到该文件的输出全都会被丢弃掉

## 举例说明

当前目录下只有 a.txt 文件

```shell
$ ls a.txt
a.txt
```

```shell
$ ls a.txt b.txt
lsd: b.txt: No such file or directory (os error 2).
```

由于没有 b.txt 这个文件，所以返回错误值，这就是标准错误输出，也就是 2 输出

```shell
$ ls a.txt b.txt 1>file.out 2>file.err
```

该命令执行后不会有任何的输出，因为返回值都被重定向到响应的文件中了

```shell
$ cat file.out
a.txt
$ cat file.err
lsd: b.txt: No such file or directory (os error 2).
```

通常，我们会将 *1>* 简写成 *>*, 即上面的命令等价于

```shell
$ ls a.txt b.txt >file.out 2>file.err
```

### 1 > &2 和 2 >&1

1 > &2 表示将 1 输出管道重定向到 2 输出管道。&2 表示 2 输出管道

2 > &1 表示将 2 输出管道重定向到 1 输出管道。&1 表示 1 输出管道

```shell
$ ls a.txt b.txt > file.out 2>&1
$ cat file.out
ls: b.txt: No such file or directory
a.txt
```

这样标注输入和标注输出都重定向到了 file.out 文件中

# nohup 命令

nohup 命令是 no hang up 不挂起的意思。用于在后台不挂断地运行命令。退出终端不会影响程序的运行

该命令默认情况下，会将命令的输出写到当前目录下的 nohup.out 文件。如果当前目录的 nohup.out 文件不可写，将会将输出写到 `$HOME/nohup.out` 文件中

## 语法格式

```tex
nohup Command [Arg ...] [&]
```

- Command 表示要后台执行的命令
- Arg 表示一些参数
- & 表示让命令在后台执行，退出终端后，命令仍然执行

