# java基础

## 数据类型

java中的数据类型分为两种：

- 基本数据类型
- 引用数据类型

### 基本数据类型：

| 类型名称     | 关键字    | 占用内存 | 取值范围                                   |
| ------------ | --------- | -------- | ------------------------------------------ |
| 字节型       | `byte`    | 1 字节   | -128~127                                   |
| 短整型       | `short`   | 2 字节   | -32768~32767                               |
| 整型         | `int`     | 4 字节   | -2147483648~2147483647                     |
| 长整型       | `long`    | 8 字节   | -9223372036854775808L~9223372036854775807L |
| 单精度浮点型 | `float`   | 4 字节   | +/-3.4E+38F（6~7 个有效位）                |
| 双精度浮点型 | `double`  | 8 字节   | +/-1.8E+308 (15 个有效位）                 |
| 字符型       | `char`    | 2 字节   | ISO 单一字符集                             |
| 布尔型       | `boolean` | 1 字节   | true 或 false                              |

**注意事项**

1. 浮点数在某些情况下并不是一个精确值，可能是一个近似值，使用浮点数作比较时需要特别注意
2. 数据范围与字节数不一定相关，比如`float`类型表示的范围比`long`类型表示的范围大，但是`float`类型只占用 4 个字节，但是`long`类型占用 8 个字节
3. 整形数字默认的数据类型是`int`，如果要使用`long`类型，需要在数字后面加上`L`
4. 浮点数中，默认是`double`类型，如果一定要使用`float`类型，则需要在数字后面加上`F`,大小写都可以，但是推荐使用大写

#### 数据类型转换

Java中数据类型转换有两种：自动类型转化和强制类型转换

##### 自动类型转换

- 特点：代码不需要进行特殊处理，自动完成
- 规则：数据范围从小到大
- 转换链： byte –> short –> int –> long –> float –> double | char –> int
- 

##### 强制类型转换

- 特点：代码需要进行特殊的处理，不能够自动完成
- 规则：范围小的数据类型 范围小的变量名 = （范围小的数据类型）原本范围大的数据
- 后果： 可能出现数据溢出和数据精度丢失的情况(一般不推荐使用)

**注意事项**

- 如果将浮点数赋值给整型数字，那么浮点数的小数部分将直接舍去，只保留整数部分
- byte / short / char 这三种数据类型进行运算时，首先会提升为 int 类型，然后在进行运算，因此在进行赋值时需要注意。例如：short + short ==> int + int = int, 因此运算结束后进行赋值时需要使用 int 类型的变量进行存储。

### 引用数据类型

引用数据类型有：字符串，数组，类，接口，Lambda

**注意事项**

- 任何数据类型与字符串类型进行连接的时候，最终的运算结果都是字符串类型

## switch 语句

switch后面小括号中的值只能是下面的数据类型：

- 基本数据类型：byte / short / char / int
- 引用数据类型：Sting(字符串类型) / enum (枚举数据类型)

## 三种循环的区别

- 如果条件判断一次也没有满足过，for 循环和 while 循环一次也不会执行，但是 do-while 循环会执行一次，也就是说，do-while 循环不管是什么条件下，都会执行一次
- for 循环的循环变量在定义子在小括号中，只能在 for 循环的作用域中进行使用；while 和 do-while 循环的循环变量定义在循环体外面，即使出了循环仍可以被使用

## 方法的调用

- 方法定义在类中，各方法定义的先后顺序无所谓
- 在静态方法中调用其他的方法，那么这些方法也必须是静态方法，否则编译无法通过
- 对于有返回值的方法，可以单独调用、打印调用、或者赋值调用
- 对于无返回值的方法，只能够单独调用，不能够打印调用和赋值调用

## 方法的重载

方法的重载：方法名一样，但是参数列表不一样

参数列表不一样的定义：

- 参数个数不一样
- 参数的类型不一样
- 参数多类型但是顺序不同

```java
int sum(int a, int b);
int sum(int a, double b); // 参数类型相同但顺序不一样
int sum(double b, int a); // 参数类型相同但顺序不一样
int sum(int a, int b, int c) // 参数个数不一样
```

方法的重载与方法的返回值和参数的名称是无关的,例如

```java
int sum(int a, int b);
int sum(int c, int d);
```

出现这种代码时，编译器会报错。因为编译器在最终生成这两个方法的汇编代码时，发现生成的方法名是一样的。

```java
int sum(int a, int b);
double sum(int a, int b);
```

这样子写也是错误的。当我们输入`sum(10, 20)` 时，编译器不知道该调用哪一个方法，因此也会报错。

### 重载和重写的对比

重写的方法，方法名一样，参数列表也一样；子类的返回值范围必须小于等于父类的返回值范围

重载的方法，方法名一样，参数列表一定不一样

## 数组初始化

### 初始化方式

```
JAVA
数据类型[] 数组名称 = new 数据类型[length]; // 声明一个length长度的数组(动态初始化)可拆分为两步
数据类型[] 数组名称 = new 数据类型[] {....}; // 编译器自动计算数组长度(静态初始化)可拆分为两步
数据类型[] 数组名称 = {....} // 静态初始化的简化写法，不可拆分为两步
JAVA
int[] arrA = new int[10];
int[] arrB = new int[] {1,2,3};
```

可以写成如下的等价形式

```java
JAVA
int[] arrA;
arrA = new int[10];

int [] arrB;
arrB = new int[]{1,2,3};
```

但是`int[] ArrC = {1,2,3}`则不能够写成`int[] arrC; arrC = {1,2,3};`

### 初始化默认值

动态初始化数组时各数据类型的默认值

| 数据类型       | 默认值   |
| -------------- | -------- |
| `int`          | `0`      |
| `float/double` | `0.0`    |
| `char`         | `\u0000` |
| `boolean`      | `false`  |
| 引用数据类型   | `null`   |

**注意事项**

静态初始化其实也是有默认值的过程，只不过系统自动马上将默认值修改为大括号中对应的值

## java内存的划分

java 将内存划分为五个部分：

- 栈区( stack )
- 堆区 ( Heep )
- 方法区( Method Area )
- 本地方法栈( Native Method Stack )
- 寄存器( Register )

栈区用于存放的都是方法中的局部变量，方法的运行一定都是在栈中完成。

堆区：凡是使用`new` 关键创建出来的东西，都存放在堆区。堆内存里面的数据都有一个十六进制的地址值，且这些数据都有一个默认值。各类型的默认值与数组初始化各类型的默认值相同

方法区: 存储`.class`的相关信息， 包含方法的信息

本地方法区：与操作系统相关

寄存器：与 CPU 相关

## 局部变量和成员变量

1. 定义的位置
   - 局部变量定义在方法的内部
   - 成员变量定义在方法的外部，直接写在类中
2. 作用域范围
   - 局部变量只能够在方法中使用
   - 成员变量在整个类中都可以使用
3. 默认值
   - 局部变量没有默认值，如果要使用，必须进行初始化
   - 成员变量如果没有进行赋值，将使用默认值。默认值的规则和数组的一样
4. 内存位置
   - 局部变量在栈区
   - 成员变量位于堆区
5. 生命周期
   - 局部变量进栈创建，出栈回收
   - 成员变量的生命周期和对应对象的生命周期相同

## 构造方法

- 如果类中没有指定构造方法，编译器默认会添加一个无参的构造方法
- 如果类中指定了构造方法，编译器将不会创建无参的构造方法。

## 匿名对象

匿名对象只能够使用唯一的一次，下次在使用必须重新创建新的对象

如果确定一个对象只需要使用一次唯一的一次，可以使用匿名对象

## 泛型

泛型只能够是引用数据类型，不能是基本数据类型。如果想使用基本数据类型，需要使用基本数据类型对应的包装类

| 基本数据类型 | 包装类      |
| ------------ | ----------- |
| `byte`       | `Byte`      |
| `short`      | `Short`     |
| `int`        | `Integer`   |
| `long`       | `Long`      |
| `float`      | `Float`     |
| `double`     | `Double`    |
| `char`       | `Character` |
| `boolean`    | `Boolean`   |

## 字符串

### 字符串的特点

1. 字符串的内容永不可变，因此字符串可以共享
2. 字符串的效果效果相当于 `char[]`，但是 java 底层使用的是`byte[]`字节数组

### 字符串常见的构造方式

```java
- public String() 创建一个空白数组
- public String(char[] array) 根据字符数组创建对应的字符串
- public String(byte[] array) 根据字节数组创建对应的字符串
- 直接创建 ==> String str = "Hello"
```

### 字符串常量池

直接使用`""`创建出来的字符串被 java 放在字符串常量池中，字符串常量池也是放在堆区中

### 字符串类常用的方法

```java
public boolean equals(Object obj); // 比较两个字符串是否相等
public int length(); // 获取字符串的长度
public String concat(String str); // 拼接字符串后返回一个新的字符串
public char charAt(int index); // 获取index位置上的字符
public int indexOf(String str); // 查找 str 在字符串中首次出现的位置，没有则返回 -1
public String substring(int index)； // 从index位置开始截取字符串直到字符串结尾
public String substring(int begin, int end); //从begin开始，end结束，左闭右开 
public char[] toCharArray(String str); // 将 str 转换成 字符串数组
public byte[] getBytes();// 获取当前字符串底层的字节数组
public String replace(CharSequence oldString, CharSequence newString); // 字符串的替换
public String[] split(String regex) // 根据regex(正则表达式)规则切割字符串，如果按照`.`切割会出现问题，得使用`\\.`, `.`在正则表达式中有特殊含义
```

`equals`推荐使用`"hello".equals(var)`,不推荐使用`var.equals("hello")`。因为 var 可能为 null

## static

- 凡是被 static 关键字修饰的属性，该类的所有对象共相同一份数据
- 被 static 修饰的成员方法不属于对象，属于类。静态方法可以直接使用类名称进行调用，不需要创建对象。在类中调用本类的静态方法，可以省略类名。
- 静态方法不能够直接访问非静态变量
- 静态方法中不能够使用 `this`关键字

### 静态代码块

```java
static{
......
}
```

特点：

- 当第一次用到类时，静态代码块执行唯一的一次
- 静态代码优先于非静态代码的执行，因此静态代码块优先于构造方式执行

典型用途：一次性的对静态成员变量进行赋值

## 数组工具类

```java
java.util.Arrays
```

常用的一些方法

```java
public static String toString(数组);
public static void sort(数组); // 排序，默认升序排序
```

```java
public static void sort(数组);
```



特点：

1. 如果是数字，默认按照升序进行排序
2. 如果是字符串，则按照字母升序的顺序进行排序
3. 如果是自定义类型，则该类型需要实现`Comparable`或者`Comparator`接口

## Math工具类

```java
java.util.Math
```

常用方法

```java
JAVA
public static double abs(double num); // 绝对值
public static double ceil(double num); // 向上取整
public static double floor(double num); // 向下取整
public static long round(double num); // 四舍五入
```

## 构造方法

- 只能够在子类的构造方法中调用父类的构造反法，并且要放在子类构造方法语句的第一句
- 调用父类构造方法使用`super()`语句，且只能位于构造方法中的第一句。
- 父类构造方法只能够调用一次，不能够多次调用

### java 对象的创建过程

父类的`cinit`(类构造器)方法 ==> 子类的`cinit`(类构造器)方法 ==> 父类的实例变量和实例代码块 ==> 父类的构造方法 ==> 子类的实例变量和实例代码块 ==> 子类的构造函数

- `cinit`方法用于检测类是否加载，确定类所需的内存的大小。这个方法用于类初始化阶段，只会在类加载进内存(`.class`文件加载进内存)时才会进行调用。`cinit`通过编译器**自动收集并执行类变量赋值表达式和静态代码块**，这就是为什么静态代码块只会在类创建的第一个对象中进行调用，其余创建的对象都不会再执行类的静态代码块, 为什么静态变量可以被该类的多个实例对象共享。该方法由 JVM 自动进行调用，不需要程序员显示的进行调用。

- 实例变量和实例代码块指的是类中位于构造方法和成员方法之外的非静态方法和静态变量，这些代码也会被编译器收集并执行，不论其位于类中的什么位置，都会被收集并先于构造方法执行

  ```java
  public class Person{
      public String name;
      public int age;
      
      public Person(String name, int age){
  		this.name = name;
          tihs.age = age;
      }
      
      {
          System.out.println("我将先于构造方法执行")；
      }
      
      public void run(){
          System.out.println("跑步");
      }
  }
  ```

  例如：`public String name `这就是实例变量， `{ System.out.println("我将先于构造方法执行") }` 这就是实例代码块

- 除了 Object 类，所有类在加载过程中都需要调用父类的构造函数，所以**在子类的构造函数中，需要使用 super()方法隐式或显式地调用父类的构造函数**。

## comparable 和 comparator

`Comparable`接口用于自定义类之间的比较，它与类绑定在一起。如果一个类没有实现`Comparable`接口，且不能对该类进行修改，又想对该类进行排序，那么可以通过`Comparator`接口进行实现

### 比较器升序降序的原理

方法返回值如果大于`0`时，后一个参数前移，使用了类似冒泡排序的思想。

## 匿名内部类

使用匿名内部类访问外部定义的变量时，变量必须使用`final`关键字进行修饰, 在高级的 java 版本中，匿名内部类即使使用了外部变量也可以不用写`final`关键字，编译器在生成`.class`文件时，会自动给被引用的变量加上`final`关键字。

为什么要加上`final`关键字：

- 匿名内部类和外部方法形成了一个闭包，因此，匿名内部类能够访问外部方法的变量
- 匿名内部类的生命周期可能比外部的类要长，因此访问外部局部变量有可能是访问不到的
- Java 语言为了实现这种特性， 只好将外部的局部变量复制一份给匿名内部类。那这样匿名内部类就可以肆无忌惮的访问外部局部变量了
- 但是这种通过赋值的形式有一个缺陷，匿名内部类不可以修改“原来的局部变量”，因为是一份“复制品”，修改复制品对原变量没什么影响
- 因此 Java 语言干脆强制要求被匿名内部类访问的外部局部变量必须是 final 的

## 代理

[代理模式](https://link.segmentfault.com/?url=https://zh.wikipedia.org/wiki/代理模式)是一种设计模式，提供了对目标对象额外的访问方式，即通过代理对象访问目标对象，这样可以在不修改原目标对象的前提下，提供额外的功能操作，扩展目标对象的功能。

简言之，代理模式就是设置一个中间代理来控制访问原目标对象，以达到增强原对象的功能和简化访问方式。

## 监听器

Web 的三大组件之一(Servlet, Filter, Listener)

## Json 与 java 的转换

- json 解析器：Jsonlib, Gson fastjson, jackson
- 使用的解析器：jackson

Jackson 所依赖的 jar 包较少 ，简单易用；与其他 Java 的 json 的框架 Gson 等相比， Jackson 解析大的 json 文件速度比较快；Jackson 运行时占用内存比较低，性能比较好；Jackson 有灵活的 API，可以很容易进行扩展和定制

### Jackson 的使用

#### ObjectMapper对象

- ObjectMapper 对象可以在字符串，流或者文件中解析 JSON ，并映射成对应的 java 对象
- ObjectMapper 对象可以将 Java 对象转换成 JSON 数据
- ObjectMapper 对象可以将 JSON 转换成自定义的对象，也可以转换成 JSON 树模型对象

#### ObjectMapper 的 readValue 方法

```java
JAVA
public <T> T readValue(File src, Class<T> valueType)
public <T> T readValue(URL src, Class<T> valueType) 
public <T> T readValue(String content, Class<T> valueType) 
public <T> T readValue(Reader src, Class<T> valueType) 
public <T> T readValue(InputStream src, Class<T> valueType) 
```

#### 字符串转换成 java 对象

```java
class Car {
    private String brand;
    private int doors;

    public String getBrand() {return brand;}

    public void setBrand(String brand) {this.brand = brand;}

    public int getDoors() {return doors;}

    public void setDoors(int doors) {this.doors = doors;}

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", doors=" + doors +
                '}';
    }
}
```

```java
String carJson ="{ \"brand\" : \"Mercedes\", \"doors\" : 5 }";
ObjectMapper mapper = new ObjectMapper();
Car car = mapper.readValue(carJson, Car.class);
System.out.println(car);
```

输出:

```java
Car{brand='Mercedes', doors=5}
```

实现的原理：

默认情况下，Jackson 通过将 JSON 字段的名称与 Java 对象中的 getter 和 setter 方法进行匹配，将 JSON 对象的字段映射到 Java 对象中的属性。Jackson 删除了getter 和 setter 方法名称的 “ get ” 和“ set ”部分，并将其余名称的第一个字符转换为小写。

例如:

名为 brand 的 JSON 字段与名为 getBrand() 和 setBrand() 的 Java getter 和 setter 方法匹配

名为 engineNumber 的 JSON 字段将与名为 getEngineNumber() 和 setEngineNumber() 的 getter 和 setter 匹配。

如果需要以其他方式将 JSON 对象字段与 Java 对象字段匹配，则需要使用自定义序列化器和反序列化器，或者使用一些 Jackson 注解。

#### JSON 字符输入流转换成 java 对象

```java
InputStream is = StrUtil.class.getClassLoader().getResourceAsStream("car.json");
ObjectMapper mapper = new ObjectMapper();
Car car = mapper.readValue(is, Car.class);
System.out.println(car);
```

其余的重载方法使用方式类似

#### java 对象转换成 JSON 数据

使用 `writeValue()`方法

```java
Car car = new Car();
car.setBrand("法拉利");
car.setDoors(2);
ObjectMapper mapper = new ObjectMapper();
mapper.writeValue(new OutputStreamWriter(new FileOutputStream("car.json")), car);
```

#### Jackson 日期转化

默认情况下，Jackson 会将 java.util.Date 对象序列化为其 long 型的值，该值是自1970年1月1日以来的毫秒数

要想将 Date 转换成指定格式的字符串可以通过在 ObjectMapper上设置 SimpleDateFormat 来指定要使用的确切 Jackson 日期格式

```java
Car car = new Car();
car.setBrand("法拉利");
car.setDoors(2);
car.setDate(new Date());
ObjectMapper mapper = new ObjectMapper();
mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd", Locale.CHINA));
mapper.writeValue(new OutputStreamWriter(new FileOutputStream("car.json")), car);
```

#### Jackson 树模型

Jackson 具有内置的树模型，可用于表示 JSON 对象。 如果不知道接收到的 JSON 的格式，或者由于某种原因而不能（或者只是不想）创建一个类来表示它，那么就要用到 Jackson 的树模型。 如果需要在使用或转化 JSON 之前对其进行操作，也需要被用到 Jackson 树模型。 所有这些情况在数据流场景中都很常见。

Jackson 树模型由 JsonNode 类表示。 您可以使用 Jackson ObjectMapper 将 JSON 解析为 JsonNode 树模型，就像使用您自己的类一样。

```java
InputStream is = StrUtil.class.getClassLoader().getResourceAsStream("car.json");
ObjectMapper mapper = new ObjectMapper();
JsonNode node = mapper.readValue(is, JsonNode.class);
System.out.println(node);
```

只需要将自定义的类换成 JsonNode 类即可

ObjectMapper 有一个 readTree() 方法可以将 JSON 解析为 JsonNode 的对象

```java
InputStream is = StrUtil.class.getClassLoader().getResourceAsStream("car.json");
ObjectMapper mapper = new ObjectMapper();
JsonNode node = mapper.readTree(is);
System.out.println(node);
```

#### 通过 JsonNode 对象访问 JSON 数据

JSON 数据

```json
{
    "brand":"Mercedes",
    "doors":5,
    "owners":["John","Jack","Jill"],
    "nestedObject":{"field":"value"}
}
```

```java
ObjectMapper mapper = new ObjectMapper();
JsonNode node = mapper.readTree(new FileInputStream("node.json"));
System.out.println(node);

JsonNode brandNode = node.get("brand");
String brand = brandNode.asText("brand");
System.out.println("brand = " + brand);

int doors = node.get("doors").asInt();
String name1 = node.get("owners").get(0).asText();
String name2 = node.get("owners").get(1).asText();
String name3 = node.get("owners").get(2).asText();

String field = node.get("nestedObject").get("field").asText();

System.out.println("doors = " + doors);
System.out.println("name1 = " + name1);
System.out.println("name2 = " + name2);
System.out.println("name3 = " + name3);

System.out.println("field = " + field);
```



#### 使用路径访问 JsonNode对象中的数据

JsonNode有一个称为 at() 的特殊方法。 at() 方法可以从 JSON 树中以给定 JsonNode 为根的任何位置访问JSON 中字段

```json
{
    "brand":"Mercedes",
    "doors":5,
    "owners":["John","Jack","Jill"],
    "nestedObject":{"field":"value"}
}
```

```java
ObjectMapper mapper = new ObjectMapper();
JsonNode node = mapper.readTree(new FileInputStream("node.json"));

String value = node.at("/nestedObject/field").asText();
System.out.println(value);
```

#### 循环 JsonNode 字段

JsonNode 类具有一个名为 fieldNames() 的方法，该方法返回一个 Iterator，可以迭代 JsonNode 的所有字段名称

```java
ObjectMapper mapper = new ObjectMapper();
JsonNode node = mapper.readTree(new FileInputStream("node.json"));
Iterator<String> it = node.fieldNames();
while(it.hasNext()){
    String fieldName = it.next();
    System.out.println(fieldName);
}
```

#### jackson 注解

| 注解       | 作用                                  |
| ---------- | ------------------------------------- |
| JsonIgnore | 告诉 Jackson 忽略 Java 对象的某个属性 |
| JsonFormat | 告诉 Jackson 转换成对应格式的数据     |

#### 添加自定义序列化器

1. 继承 JsonSerializer 类
2. 通过 Module 类设置自定义的 JsonSerializer 

```java
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;
import com.yihuo.device.feign.common.enums.IEnum;

import java.io.IOException;

public class IEnumSerializer extends JsonSerializer<IEnum> {
    @Override
    public void serialize(IEnum value, JsonGenerator gen, SerializerProvider serializers) throws IOException {
        gen.writeStartObject();
        gen.writeNumberField("code", value.getCode());
        gen.writeStringField("message", value.getMessage());
        gen.writeEndObject();
    }
}
```

```java
import com.fasterxml.jackson.databind.Module;
import com.fasterxml.jackson.databind.module.SimpleModule;
import com.yihuo.device.feign.common.enums.IEnum;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class JacksonConfiguration {

    @Bean
    Module module() {
        SimpleModule module = new SimpleModule();
        module.addSerializer(IEnum.class, new IEnumSerializer());
        return module;
    }
}
```

#### RabbitMQ 自定义序列化器

1. 自定义序列化器
2. 在 ObjectMapper 对象中注入自定义序列化器
3. 将 ObjectMapper 对象注入到 MessageConverter 中
4. 将 MessageConverter 注入到 IOC 容器中

```java
@Configuration
public class RabbitConfiguration {
	@Bean
    RabbitListenerContainerFactory<?> rabbitListenerContainerFactory() {
        SimpleRabbitListenerContainerFactory factory = new SimpleRabbitListenerContainerFactory();
        factory.setConnectionFactory(connectionFactory());
        factory.setMessageConverter(messageConverter());
        return factory;
    }

    @Bean
    MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter(objectMapper());
    }

    @Bean
    ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        SimpleModule module = new SimpleModule();
        module.addSerializer(JobState.class, new JobStateSerializer());
        module.addSerializer(DeviceState.class, new DeviceStateSerializer());
        module.addSerializer(ChannelState.class, new ChannelStateSerializer());
        mapper.registerModule(module);
        return mapper;
    }
}

public class JobStateSerializer extends JsonSerializer<JobState> {
    @Override
    public void serialize(JobState value, JsonGenerator gen, SerializerProvider serializers) throws IOException {
        gen.writeNumber(value.getCode());
    }
}
```