# JVM 监控工具

## JDK 自带工具

| 命令     | 类型     | 描述                                        |
| -------- | -------- | ------------------------------------------- |
| jps      | 命令行   | 列出系统正在运行的 JVM 进程                 |
| jinfo    | 命令行   | 查看 JVM 的各种 flags，如年轻代配置的大小   |
| jconsole | 图形界面 | 查看 JVM 的基本信息                         |
| jstat    | 命令行   | 查看 JVM 编译，垃圾回收等信息               |
| jstack   | 命令行   | 查看 JVM 线程栈和锁的情况                   |
| jcmd     | 命令行   | JVM 命令行调试工具，用于向 JVM 发送调试命令 |
| jmap     | 命令行   | 查看堆空间信息                              | 

### jstat

使用 jstat 工具可以监控 Java 程序的实时运行情况：

1. JVM Eden 区，Survivor 区, 老年代的内存使用情况
2. YoungGC, FullGC 执行的次数以及执行的时间
3. .....

jstat 通过不同的 option， 显示不同的信息，jstat 支持的 option 可通过 `jstat -options` 命令进行查看：

- -class: 显示 ClassLoad 相关的信息
- -compiler: 显示 JIT 编译相关的信息
- -gc: GC 相关的堆信息，FullGc, YoungGc 执行的次数以及时间
- -gccapacity: 显示各代对堆的使用情况
- -gccause: 同 -gcutil 选项，同时会显示最后一次 GC 的原因
- -gcmetacapacity: 显示 metaspace 空间的大小
- -gcnew: 显示新生代的堆信息
- -gcnewcapacity: 显示新生代堆的容量
- -gcold: 显示老年代的堆信息
- -gcoldcapacity: 显示老年代堆的容量
- -gcutil: 显示垃圾收集信息
- -printcompilation: 输出 JIT 编译的方法信息

其中比较常用的命令是：`jstat -gc <pid>`, pid 是 jps 命令显示的 Java 程序的 ID。通过 `-gc` 选项，可以获得大部分信息

命令格式：

```text
jstat -gc <pid> [<interval> <count>]
```

- pid: jps 命令输出的 java 程序进程 ID
- interval: 输出间隔，单位 ms
- count: 输出次数，当 count <= 0 时，会无限输出

```shell
$ jstat -gc 4100 1000 10
```

上面的命令每隔 1s 输出一次 GC 信息，一共会输出 10 次

### jstack

jstack 可以查看线程的栈信息。我们可以从栈信息中得到：

- 线程名称
- 线程编号
- 线程优先级
- 系统线程的优先级
- 线程 ID
- native 线程 ID
- 线程的状态

通过 jstack 命令可以排查一些死锁，CPU 占用高的线程。