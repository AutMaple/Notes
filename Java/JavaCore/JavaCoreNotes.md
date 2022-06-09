# Java Core Notes

# Java Command

The java program launches the Java virtual machine. It executes the bytecodes that the compiler placed in the class file

# Code Points and Code Units

Nowadays, some Unicode characters can be described with one char value, and other Unicode characters require two char values.

*A code point* is a code value that is associated with a character in an encoding scheme.

码点表示某个字符在对应编码格式下的值，如字符 A 在 Unicode 编码中用 *U+0041* 来表示，那么 *U+0041* 就是一个码点，码点是代号，并不是在计算机中实际存储的值，比如 *U+FFFFF*

The characters in the basic multilingual plane are represented as 16-bit values, *called code units*.

在计算机中存储的 16 位二进制值用于代表一个码元。由于一个码元由 16 位组成，因此能够表示的范围最大是 *0xFFFF*, 如果用编码来表示所有的 Unicode 字符，那么码点超过 *U+FFFF* 的 Unicode 字符就需要使用两个码元来表示了

In the Unicode standard, code points are written in hexadecimal and prefixed with *U+*, such as U+0041 for the code point of the Latin letter A. Unicode has code points that are grouped into 17 code planes. The first code plane, called *the basic multilingual plane*, consists of the “classic” Unicode characters with code points *U+0000* to *U+FFFF*. Sixteen additional planes, with code points *U+10000* to *U+10FFFF*, hold the supplementary characters. The UTF-16 encoding represents all Unicode code points in a variable-length code.

Java strings are sequence of *char* values. the *char* data type is a code unit for representing Unicode code points in the UTF-16 encoding. The most commonly used Unicode characters can be represented with a single code unit. The supplementary characters require a pair of code units.

Java 字符串是由若干个 char 组成的字符序列。而 char 类型中存储的是 UTF-16 编码中的一个码元。UTF-16 编码中的一个码元用于代表 Unicode 编码中的一个码点。常用的 Unicode 字符一个 UTF-16 码元就可以表示，其他的字符则需要两个码元来表示。

# Unicode 编码

Unicode escape sequences are processed before the code is parsed. For
example, `"\u0022+\u0022"` is not a string consisting of a plus sign surrounded by quotation marks (U+0022). Instead, the `\u0022` are converted into `"` before parsing, yielding `""+""`, or an empty string.

A **code point** is a code value that is associated with a character in an
encoding scheme

Unicode has code points that are grouped into 17 code planes. The first code plane, called the *basic multilingual plane*, consists of the “classic” Unicode characters with code points `U+0000` to `U+FFFF`. Sixteen additional planes, with code points `U+10000` to `U+10FFFF`, hold the supplementary characters.

# UTF-16 编码

The UTF-16 encoding represents all Unicode code points in a variable-length code. The characters in the basic multilingual plane are represented as 16-bit values, called **code units**. The supplementary characters are encoded as consecutive pairs of code units. Each of the values in such an encoding pair falls into a range of 2048 unused values of the basic multilingual plane, called the surrogates area (`U+D800` to `U+DBFF` for the first code unit, `U+DC00` to `U+DFFF` for the second code unit). This is rather clever, because you can immediately tell whether a code unit encodes a single character or it is the first or second part of a supplementary character. `0xDBFF - 0xD800 = 1023`

[Encoding Algorithm Description](https://datatracker.ietf.org/doc/html/rfc2781)

The rules for how characters are encoded in UTF-16 are:

   -  Characters with values less than 0x10000 are represented as a
      single 16-bit integer with a value equal to that of the character
      number.

   -  Characters with values between 0x10000 and 0x10FFFF are
      represented by a 16-bit integer with a value between `0xD800` and
      `0xDBFF` (within the so-called high-half zone or high surrogate
      area) followed by a 16-bit integer with a value between `0xDC00` and
      `0xDFFF` (within the so-called low-half zone or low surrogate area).

   -  Characters with values greater than `0x10FFFF` cannot be encoded in
      UTF-16.

Note: **Values between 0xD800 and 0xDFFF are specifically reserved for use with UTF-16, and don't have any characters assigned to them.**

In Java, the `char` type describes a code unit in the UTF-16 encoding.

**I strongly recommend against using the char type in your programs unless you are actually manipulating UTF-16 code units**

The methods in the Math class use the routines in the computer’s floating-point unit for fastest performance

If completely predictable results are more important than performance, use the StrictMath class instead. It implements the algorithms from the “Freely Distributable Math Library” (www.netlib.org/fdlibm), guaranteeing identical results on all platforms.

![java 基本数据类型转换图](/home/autmaple/Documents/Notes/Attachment/image-20220601213204426.png)

The solid arrow denotes conversions without information loss, The dotted arrows denotes conversions that may lose precision.

When two values are combined with a binary operator (such as n + f where n is an integer and f is a floating-point value), both operands are converted to a common type before the operation is carried out.
• If either of the operands is of type double, the other one will be converted to a double.
• Otherwise, if either of the operands is of type float, the other one will be converted to a float.
• Otherwise, if either of the operands is of type long, the other one will be converted to a long.
• **Otherwise, both operands will be converted to an int**.

```java
int x = 0;
x += 3.5
```

等价于

```java
int x = 0;
x = (int)(x + 3.5)
```

A switch expression with an integer or String operand must always have a
default since it must yield a value, no matter what the operand value is. The right-hand argument of the shift operators is reduced modulo 32 (unless the left-hand argument is a long, in which case the right-hand argument is reduced modulo 64). For example, the value of 1 << 35 is the same as 1 << 3 or 8

# 浮点数运算精度问题

One of the stated goals of the Java programming language is portability. A computation should yield the same results no matter which virtual machine executes it. For arithmetic computations with floating-point numbers, it can be surprisingly difficult to achieve this portability. The double type uses 64 bits to store a numeric value, but some processors use 80-bit floating-point registers. These registers yield added precision in intermediate steps of a computation.

But the result may be *different* from a computation that uses 64 bits throughout. For perfect portability, the initial specification of the Java virtual machine mandated that all intermediate computations must be truncated to 64 bit. The numeric community hated it. The computations were *slower* because the truncation operations took time on popular processors. For that reason, the Java programming language was updated to recognize the conflicting demands for optimum performance and perfect reproducibility. Virtual machine designers were permitted to use extended precision for intermediate computations. *However, methods tagged with the **`strictfp`** keyword had to use strict floating-point operations that yield reproducible results.*

Processors have become more flexible, and they can now carry out 64-bit arithmetic efficiently. As of Java 17, the virtual machine is again required to carry out strict 64-bit arithmetic, and *the **`strictfp`** keyword is now obsolete.*

# Strings

only `string literals` are shared, not strings that are the result of operations like + or substring. Therefore, never use == to compare strings lest you end up with a program with the worst kind of bug—an intermittent one that seems to occur randomly.

## String length

The length method yields the number of code units required for a given string in the UTF-16 encoding.

To get the true length—that is, the number of code points - call *codePointCount* method

```java
"java".codePointCount(0, "Java".length())
```

**String 的大部分方法操作的基本单位是码元(code unit)，而不是码点(code point)，操作码元在大部分的情况下是没有问题的，除非操作的字符串中含有需要多个码元才能够表示的字符。如果你不能容忍这种情况的发生，那么就使用 String 类操作码点相关的方法**

### 获取第 i 个位置的码点

```java
String greeting = "Hello World";
int index = greeting.offsetByCodePoint(0, i);
int cp = greeting.codePointAt(index)；
```

## String 在 Java 9 中的表示

The virtual machine does not have to implement strings as sequences of code units. A more compact representation is used as of Java 9. **Strings that hold only single-byte code units use a byte array, and all others a char array.**

# 终端输入密码

*Scanner* 类并不适合进行密码的输入，因为它的输入是任何人都可以看见的。如果想让密码在输入时隐藏起来，可以使用 *Console* 类

```java
 Console console = System.console();
 String username = console.readLine("User Name: ");
 char[] password = console.readPassword("Password: ");
```

After you are done processing the password, you should immediately overwrite the array elements with a filler value.

在处理完密码之后，因该立刻使用填充字符来覆盖数组中的元素，如使用 *\** 来进行替换

# printf 格式化字符(Conversion)

| 格式符   | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| %d       | 十进制类型                                                   |
| %x or %X | 十六进制，如果相对十六进制的数进行更为详细的转换，可以使用 HexFormat 类 |
| %o       | 八进制                                                       |
| %f or %F | 浮点数格式化，可以设置保留小数点后多少位，如 %4.2f 总共 4 位数字，保留 2 位小数 |
| %e or %E | 使用科学计数法                                               |
| %g or %G | 通用的浮点数转换符，是 %f 和 %e 的简写形式                   |
| %a or %A | 十六进制浮点数转换                                           |
| %s or %S | 字符串转换符                                                 |
| %b or %B | 布尔类型转换符                                               |
| %h or %H | 输出哈希值                                                   |
| %%       | 输出 %                                                       |
| %n       | 格式化平台相关的换行符                                       |
| %c or %C | 字符类型转换符                                               |

我们可以使用 *%s* 来转换任何的对象，如果转化的对象实现了 *Formattable* 接口，那么在转换时就会调用它的 **`formatTo`** 方法，否则，就调用它的 *toString* 方法

我们可以使用 *flags* 来控制格式化输出的样式，例如可以使用 *","* 来为数字添加分组符号

```java
 System.out.printf("%,.2f", 10000.0 / 3.0) // 输出: 3,333.33
```

同时 *flags* 可以多个同时使用，例如 *%,(.2f* 将数字使用 *”,“* 进行分组，同时将负数使用小括号包裹起来

```java
System.out.printf("%,(.2f", -10000.0 / 3.0) // 输出:(3,333.33)
```

## printf 中的 flags

| Flag                   | description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| +                      | 打印正数符号，例如: 33 则输出 +33                            |
| Space                  | 在正数前面添加一个空格                                       |
| 0                      | 位置没满，用零补充，例如: "%08.2f" => 3333.33 => 003333.33   |
| \-                     | 左对齐，默认是右对齐                                         |
| (                      | 将负数放在小括号里面                                         |
| ,                      | 添加 "," 作为分组符号，常用于数字格式化输出，如 33333 => 33,333 |
| \# (for f format)      | 总是保留一个十进制点，如 printf("%,#.0f", 3333.0)  => 3,333. |
| \# (for x or 0 format) | 为 16 进制添加 0x 前缀，为 8 进制添加 0 前缀                 |
| $                      | 指定参数的索引进行格式化，printf("%1$f, %1$s", 3333.0) => 3333.000000, 3333.0 |
| <                      | 格式化上一个参数的值，printf("%d, %<x", 32) => 32, 20        |

注意：格式化是特定于本地语言环境的，例如，在德国，分组符号就不是逗号 `,`, 而是句号 `.`

## String.format 方法

该方法用于格式化一个字符串，并将格式化后的字符串进行返回

# Big Numbers

BigInteger 类用于表示任意精度的整数

BigDecimal 类用于表示任意精度的浮点数

**注意**

在使用 BigDecimal 类的时候，尽可能使用带有 String 类型参数的构造方法进行初始化。因为带有 double 参数的构造方法是不准确的, 有些数字 double 无法精确的表示

# var 关键字

Note that the var keyword can only be used with local variables inside methods

# JIT

The just-in-time compiler watches for calls to methods that are short, commonly called, and not overridden, and optimizes them away.

## Call By Value VS Call By Reference
The term *call by value* means that the method gets just the value that the caller provides.

The term *call by reference* means that the method gets the location of the variable that the caller provides

The Java programming language always uses **call by value**. That means that the method gets a copy of all parameter values.

## Overloading 重载
Overloading occurs if several methods have the same name but different parameters.It picks the correct method by matching the parameter types in the headers of the various methods with the types of the values used in the specific method call.
### Signature
To completely describe a method, you need to specify its name together with its parameter types. This is called the signature of the method. The return type is not part of the method signature
## Finalize 方法
**Do not use the *finalize* method for cleanup.** That method was intended to be called ***before*** the garbage collector sweeps away an object. However, you simply cannot know when this method will be called, and it is now deprecated

## pacakge
If you don't put a package statement in the source file, then the classes in that source file belong to the ***unnamed package***
Note again that the compiler operates on files (with file separators and an extension .java), whereas the Java interpreter loads a class (with dot separators)
注意: java 编译器操作文件时使用路径分隔符(如 linux 中使用 / )和 `.java` 扩展名。而 Java 解释器加载 class 文件使用的是句号 `.` 分隔符

### 类编译和执行
The compiler does not check the directory structure when it compiles source files. For example, suppose you have a source file that starts with the directive
```Java
package com.mycompany;
```


You can compile the file even if it is not contained in a subdirectory `com/mycompany` The source file will compile without errors if it doesn't depend on other packages. However, the resulting program will not run unless you first move all class files to the right place. The virtual machine
won't find the classes if the packages don't match the directories
也就是说，编译 java 文件可以在任何地方编译，但是如果要运行程序，这需要将工作目录切换到项目的根路径上
The javac compiler always looks for files in the current directory, but the java virtual machine launcher only looks into the current directory if the “.”directory is on the class path. If you have no class path set, it's not a problem— the default class path consists of the “.” directory.
### 类查找流程
Suppose the virtual machine searches for the class file of the `com.horstmann.corejava.Employee` class. It first looks in the Java API classes. It won't find the class file there, so it turns to the class path.

The compiler has a **harder time** locating files than does the virtual machine. If you refer to a class without specifying its package, the compiler first needs to find out the package that contains the class. It consults all import directives as possible sources for the class. For example, suppose the source file contains directives

```java
import java.util.*;
import com.horstmann.corejava.*;
```

and the source code refers to a class `Employee`. The compiler then tries to find `java.lang.Employee` (because the `java.lang` package is always imported by default), `java.util.Employee`, `com.horstmann.corejava.Employee`, and `Employee` in the current package.It searches for each of these classes in all of the locations of the class path. It is a compile-time error if more than one class is found. (Fully qualified class names must be unique, **so the order of the import statements doesn’t matter.**)

The compiler goes one step further. It looks at the source files to see if the source is newer than the class file. If so, the source file is recompiled automatically

If you import a class from the current package, the compiler searches **all** source files of the current package to see which one defines the class

**As of Java 9, classes can also be loaded from the module path**
