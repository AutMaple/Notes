## Java 的生命周期

java代码通过 javac 程序编译源码得到`.class`的字节码文件，得到的`.class` 文件通过**类加载机制**载入到Java虚拟机中进行运行

`.class`字节码文件是java 平台能够处理的最小功能单位，也是把新代码传递给**运行中的程序**的唯一方式

## 类加载机制

## 字节码是什么？

可以将字节码视为是 Java 虚拟机 JVM 执行的机器码，它与 CPU 执行的机器码不一样，计算机科学家将字节码视为是一种中间形式，位于源码和机器码中间

字节码的目的是提供一种能够让 JVM 高效执行的格。将 JVM 当作是计算机中的计算机

不管在那种设备中生成，字节码的格式都是一样的，其中也包括设备使用的顺序，并且字节码使用的始终是大端序(big-endian)，因此字节码是**设备无关的**。

## 大端序和小端序

大端序 (big-endian),小端序(little-endian)

大端序：讲一个多位数的低位放在较大的地指出，高位放在较低的地址处

小端序：将一个多位数的低位放在较小的地址处，高位放在较大的地址处

## javac 算不算编译器?

编译器一般生成的是机器码，但是javac 生成的是和机器码不太一样的字节码

字节码文件(.class文件)有点像对象文件（例如 Windows 中的 .dll 文件，或 Unix 中的 .so 文件），人类肯定读不懂

在计算机科学理论的术语中，javac 非常像编译器的“前半部分”，它生成的中间表现形式可以进一步处理，生成机器码。

通常把“编译”看作一个单独的术语，表示 JIT 编译，因为只有 JIT 编译才会生成机器码

## JIT 编译

IT[编译器](https://baike.baidu.com/item/编译器)，英文写作Just-In-Time Compiler，中文意思是[即时编译器](https://baike.baidu.com/item/即时编译器/18428531)

JIT是一种提高程序运行效率的方法。通常，程序有两种运行方式：静态编译与动态解释

静态编译的程序在执行前全部被翻译为机器码，而动态解释执行的则是一句一句边运行边翻译

在Java编程语言和环境中，即时[编译器](https://baike.baidu.com/item/编译器)（JIT [compiler](https://baike.baidu.com/item/compiler)，just-in-time compiler）是一个把Java的[字节码](https://baike.baidu.com/item/字节码)（包括需要被解释的指令的程序）转换成处理器可以直接运行的指令（机器码）的程序

## JVM

JVM 基本上算是一种解释器, 它通过 JIT 大幅提升性能

## 浮点类型

java 中的实数类型包括32 位的float类型 和64位的double 类型，同时这两种类型都符合 IEEE 754-1985 标准

当声明一个浮点数时，默认是double类型，如果想声明一个float 类型的数，可以在浮点数后面加上字母f或者F ，如`3.14f`

浮点数字面量不能使用十六进制、二进制或八进制表示

由于本质上的限制，大多数实数都不能使用有限的位数进行精确表示。因此，float 和 double 类型都只能够表示实数类型的近似值

float 至少有6位的有效数字，double至少有15位的有效数字

float 和 double 特殊的四个值：正无穷大(inf)，负无穷大(-inf)，零以及NaN(Not a Number)。当超出实数的上限时，得到的是正无穷大；当超出实数的下限时，得到的是**零**

java 的浮点数类型区分 **正零**和**负零** ，具体取哪个值取决于从哪个方向上出现下溢。负零和正零在表现上几乎没有什么区别,并且通过`==` 运算符可以知道，它们是相等的。区分正零和负零的一种方式就是将它们当作除数，如果是正零，得到的就是正无穷大，反之是负无穷大

NaN: 当运算不合法时，得到的就是 NaN，例如`0.0/0.0` , 这个运算得到的就是 NaN，但是实数运算是可以除零的，只不过得到的结果是无穷大，除了`0.0/0.0` 。NaN 不是一个数，因此它不等于任何一个数，包括它自己，即 `NaN ≠ NaN`

```java
double inf = 1.0/0.0 // 无穷大
double neginf = -1.0/0.0 // 负无穷大
double negzeo = -1.0/inf // 负零
double NaN = 0.0/0.0 // NaN
```

Java 浮点数类型能处理到无穷大的上溢以及到零的下溢，因此浮点数运算**从不抛出异常**，就算执行非法运算也没事，例如零除以零，或计算负数的平方根。

在 Java 中，布尔值是 **唯一**一种不能和其他基本类型之间相互转换的基本类型

将浮点数转换成小数的时候，小数部分会丢弃

## 算术运算符

- 加号( + ): 可以用来计算两个数的和，也可以用于连接字符串，如果两个操作数有一个是字符串，那么结果也是字符串
- 除号( / ): 如果两个数都是整数，那么返回的结果会丢弃余数，只保留整数部分；如果两个整数相除，除数为零的话，会抛出ArithmeticException 异常，但是如果是浮点数，除零则不会报错，得到的结果是无穷大或者NaN
- 求模运算( % ): 求模运算的结果的符号与第一个操作数的符号相同。操作数可以是整数，也可以是浮点数。如果操作数是整数，计算零的模数会抛出 ArithmeticException 异常。如果操作数是浮点数，计算 0.0 的模数得到的结果是 NaN；计算无穷大和任何数的模数得到的结果也是 NaN。

## 字符串连接运算符

如果 + 号的两个操作数中有一个是字符串，另一个操作数也会转换成字符串。因此，如果加法和字符串连接结合在一起使用，要把加法表达式放在括号中。如果不这么做，加号会被理解成连接运算符。

**Java 解释器原生支持把所有基本类型转换成字符串**。对象转换成字符串时，调用的是对象的 toString() 方法。有些类自定义了 toString() 方法，所以这些类的对象可以使用这种方式轻易地转换成字符串。数组转换成字符串时会调用原生的 toString() 方法，不过可惜，这个方法没有为数组的内容提供有用的字符串形式。

## 运算符返回的类型

基本数据类型：整型，浮点型，字符型以及布尔类型

**算数运算符可以用于整数、浮点数和字符，即除了布尔类型之外的所有基本数据类型**

对算术运算符、递增和递减、位运算符和位移运算符来说

如果至少有一个操作数是 double 类型，返回值就是 double 类型

如果至少有一个操作数是 float 类型，返回值是 float 类型

如果至少有一个操作数是 long 类型，返回值是 long 类型

除此之外都返回 int 类型的值，就算两个操作数都是 byte、short 或 char 类型，也会放大转换成 int 类型

## StringBuilder 和 StringBuffer

- StringBuilder 不是线程安全的，StringBuffer 是线程安全的

- StringBuilder 和 StringBuffer 对象都可以多次被修改，而不会产生新的对象
- 通常 StringBuilder 的速度快一些，在不要求同步的场景中，通常使用 StringBuilder。如果要求同步的场景中对字符串进行操作就只能够使用 StringBuffer

