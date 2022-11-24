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

# this 指针问题

在继承关系中，不管父类还是子类，这些类里面的 this 都代表了最终 new 出来的那个类的实例对象，所以在父类中你可以用 this 获取到子类的信息！

# Type 接口

Type 是 Java 语言中 `所有类型的公共父接口`，其从 JDK5 开始引入，引入的目的主要是为了支持 `泛型`, Type 接口和 泛型、反射息息相关

## Java 中的所有类型

- raw type: 原始类型，对应 `Class`
  - 即我们通常说的引用类型，包括普通的类，例如 `String.class`、`List.class`
  - 也包括数组 (`Array.class`)、接口(`Cloneable.class`)、注解 (`Annotation.class`)、枚举 (`Enum.class`)等
- primitive types: 基本类型，对应 `Class`
  - 包括 `Built-in` 内置类型，例如 `int.class`、`char.class`、`void.class`
  - 也包括 `Wrappers` 内置类型包装类型，例如 `Integer.class`、`Boolean.class`、`Void.class`
- parameterized types 参数化类型，对应 `ParameterizedType`
  - 带有类型参数的类型，即常说的泛型，例如 `List<T>`、`Map<Integer, String>`、`List<? extends Number>`
  - 实现类 `sun.reflect.generics.reflectiveObjects.ParameterizedTypeImpl`
- type variables: 类型变量类型，对应 `TypeVariable<D>`
  - 即参数化类型 ParameterizedType 中的 E、K 等类型变量，表示泛指任何类
  - 实现类 `sun.reflect.generics.reflectiveObjects.TypeVariableImpl`
- array types 泛型数组类型，对应 `GenericArrayType`
  - 元素类型是参数化类型或者类型变量的泛型数组类型，例如 `T[]`
  - 实现类 `sun.reflect.generics.reflectiveObjects.GenericArrayTypeImpl`

> Type 接口的另一个子接口 `WildcardType` 代表 `通配符表达式类型`，或 `泛型表达式类型`，比如 `?`、`? super T`、`? extends T`，他并不是 Java 类型中的一种。

```java
public class Test {
    public static void main(String[] args) throws NoSuchMethodException {
        new Test().showType();
    }

    private void showType() throws NoSuchMethodException {
        Class<List> clazz = List.class;
        Method method = Test.class.getMethod("test", int.class, Boolean.class, clazz, clazz, clazz, clazz, clazz, Map.class);
        Type[] genericParameterTypes = method.getGenericParameterTypes(); // 按照方法参数声明的顺序返回参数 Type 数组
        for (Type type : genericParameterTypes) {
            if (type instanceof ParameterizedType) {
                ParameterizedType parameterizedType = (ParameterizedType) type;
                Type[] types = parameterizedType.getActualTypeArguments(); // 返回该泛型参数实际的类型数组
                for (int i = 0; i < types.length; i++) {
                    System.out.println("位置:" + i + " ：" +  getTypeInfo(types[i]));
                }
            } else {
                System.out.println(" " + getTypeInfo(type));
            }
        }
    }

    private String getTypeInfo(Type type) {
        String typeName = type.getTypeName();
        Class<? extends Type> clazz = type.getClass();
        Class<?>[] interfaces = clazz.getInterfaces();
        StringBuilder typeInterface = new StringBuilder();
        for (Class<?> clazzType : interfaces) {
            typeInterface.append(clazzType.getSimpleName()).append(", ");
        }
        return  "类型名称: " + typeName + " --- Type 类型: " + clazz.getSimpleName() + " --- Type实现的接口: " + typeInterface;
    }


    public <T> void test(int i,
                         Boolean b,
                         List<String> a1,
                         List<ArrayList<String>> a2,
                         List<T> a3,
                         List<? extends Number> a4,
                         List<ArrayList<String[]>> a5,
                         Map<Boolean, Integer> a6) {

    }
}
```

输出：

```java
类型名称: int --- Type 类型: Class --- 类型实现的接口: Serializable, GenericDeclaration, Type, AnnotatedElement, 
类型名称: java.lang.Boolean --- Type 类型: Class --- 类型实现的接口: Serializable, GenericDeclaration, Type, AnnotatedElement, 
位置:0 ：类型名称: java.lang.String --- Type 类型: Class --- 类型实现的接口: Serializable, GenericDeclaration, Type, AnnotatedElement, 
位置:0 ：类型名称: java.util.ArrayList<java.lang.String> --- Type 类型: ParameterizedTypeImpl --- 类型实现的接口: ParameterizedType, 
位置:0 ：类型名称: T --- Type 类型: TypeVariableImpl --- 类型实现的接口: TypeVariable, 
位置:0 ：类型名称: ? extends java.lang.Number --- Type 类型: WildcardTypeImpl --- 类型实现的接口: WildcardType, 
位置:0 ：类型名称: java.util.ArrayList<java.lang.String[]> --- Type 类型: ParameterizedTypeImpl --- 类型实现的接口: ParameterizedType, 
位置:0 ：类型名称: java.lang.Boolean --- Type 类型: Class --- 类型实现的接口: Serializable, GenericDeclaration, Type, AnnotatedElement, 
位置:1 ：类型名称: java.lang.Integer --- Type 类型: Class --- 类型实现的接口: Serializable, GenericDeclaration, Type, AnnotatedElement, 
```

## ParameterizedType 接口

接口的定义如下：

```java
public interface ParameterizedType extends Type {
    Type[] getActualTypeArguments();
    Type getRawType(); 
    Type getOwnerType();
}
```

- getActualTypeArguments()

  主要用于获取泛型参数的类型，可以返回 0 个 或者多个。 如一个 `Map<String, Device>` 类型的参数，那么调用该方法时，获取到的就是 String 和 Device 组成的 Type 数组，并且在数组中的顺序是按照泛型参数声明的顺序

- getRawType()

  获取声明泛型参数的 Type， 简单的说就是获取 `<>` 前面的类型。例如 `List<Device>` Type 调用该方法时，返回的就是 `List`

- Type getOwnerType()

  Returns a Type object representing the type that this type is **a member of**. For example, if this type is `O<T>.I<S>`, return a representation of  `O<T>`. If this type is a top-level type, **null** is returned.

```java
private void testParameterizedType() throws NoSuchMethodException {
    Method method = Test.class.getMethod("testType", Map.Entry.class);
    ParameterizedType parameterizedType = (ParameterizedType) method.getGenericParameterTypes()[0];
    
    System.out.println("getOwnerType  " + getTypeInfo(parameterizedType.getOwnerType()));
    System.out.println("getRawType  " + getTypeInfo(parameterizedType.getRawType()));
    
    Type[] types = parameterizedType.getActualTypeArguments();
    for (Type type : types) {
        System.out.println(getTypeInfo(type));
    }
}

public <T> void testType(Map.Entry<String, T> mapEntry) {
}
```

输出：

```java
getOwnerType  【java.util.Map】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
getRawType  【java.util.Map$Entry】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
【java.lang.String】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
【T】    【TypeVariableImpl】    【TypeVariable,】
```

## TypeVariable 接口

TypeVariable表示类型变量类型，如参数化类型中的 E、K 等类型变量，表示泛指任何类。

```java
// TypeVariable is the common superinterface for type variables of kinds 各种类型变量
public interface TypeVariable<D extends GenericDeclaration> extends Type

// Type Parameters D: the type of generic declaration that declared the underlying type variable
```

**方法**

- `Type[] getBounds()`
  Returns an array of Type objects representing the **upper bound(s)** of this type variable.
  Note that if no upper bound is explicitly declared, the upper bound is **Object**.
- `D getGenericDeclaration()` 。
  Returns the `GenericDeclaration` object representing the `generic declaration` declared this type variable 声明此类型变量的泛型声明
- `String getName()`
  Returns the name of this type variable, as it occurs in the source code.

**测试代码**

```java
private void testTypeVariable() throws NoSuchMethodException {
    Method method = Test.class.getMethod("testType");
    TypeVariable<?>[] typeVariables = method.getTypeParameters(); //返回泛型声明的 TypeVariable 数组
    
    for (int i = 0; i < typeVariables.length; i++) {
        TypeVariable<?> typeVariable = typeVariables[i];
        Type[] bounds = typeVariable.getBounds();
        GenericDeclaration genericDeclaration = typeVariable.getGenericDeclaration(); //【public void Test.test()】
        boolean isSameObj = genericDeclaration.getTypeParameters()[i] == typeVariable; // true，是同一个对象
        
        System.out.println(getTypeInfo(typeVariable));
        for (Type type : bounds) {
            System.out.println("    " + getTypeInfo(type));
        }
    }
}

public <T extends List<String>, U extends Integer, Int> void testType() {
}
```

```txt
【T】    【TypeVariableImpl】    【TypeVariable,】
    【java.util.List<java.lang.String>】    【ParameterizedTypeImpl】    【ParameterizedType,】
【U】    【TypeVariableImpl】    【TypeVariable,】
    【java.lang.Integer】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
【Int】    【TypeVariableImpl】    【TypeVariable,】
    【java.lang.Object】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
```

## GenericArrayType 泛型数组类型

GenericArrayType 表示泛型数组类型，比如 `T[]`。注意，这不是我们说的一般数组，而是表示一种【元素类型是`参数化类型`或者`类型变量`】的数组类型。

```java
// GenericArrayType represents an array type whose component type is either a parameterized type or a type variable 类型变量.
public interface GenericArrayType extends Type{
    // Returns a Type object representing the **component type** of this array
 	Type  getGenericComponentType(); // 获取泛型数组中元素的类型
}
```

**测试代码**

```java
private void testGenericArrayType() throws NoSuchMethodException {
    Method method = Test.class.getMethod("testType", Object[].class, String[].class, List.class);
    Type[] types = method.getGenericParameterTypes(); //按照方法参数声明顺序返回参数的 Type 数组
    for (Type type : types) {
        System.out.println(getTypeInfo(type));
        if (type instanceof GenericArrayType) {
            GenericArrayType genericArrayType = (GenericArrayType) type;
            System.out.println("    " + getTypeInfo(genericArrayType.getGenericComponentType()));
        }
    }
}

// 只有第一个参数是【泛型数组】类型
public <T> void testType(T[] a1, String[] a2, List<T> a3) {
}
```

```txt
【T[]】    【GenericArrayTypeImpl】    【GenericArrayType,】
    【T】    【TypeVariableImpl】    【TypeVariable,】
【java.lang.String[]】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
【java.util.List<T>】    【ParameterizedTypeImpl】    【ParameterizedType,】
```

### WildcardType 通配符类型

WildcardType 代表通配符类型，或泛型表达式类型，比如 `?`、`? super T`、`? extends T`，他并不是 Java 类型中的一种。

```java
// WildcardType represents a wildcard type expression 通配符类型表达式, such as ?, ? extends Number, or ? super Integer.
public interface WildcardType extends Type
```

**方法**

- `Type[] getUpperBounds()`
  Returns an array of Type objects representing the **upper bound(s)** of this type variable.
  Note that if no upper bound is explicitly declared, the upper bound is **Object**.
- `Type[] getLowerBounds()`
  Returns an array of Type objects representing the **lower bound(s)** of this type variable.
  Note that if no lower bound is explicitly declared, the lower bound is the type of **null**. In this case, a `zero length array` is returned.

```java
private void testWildcardType() throws NoSuchMethodException {
    Method method = Test.class.getMethod("testType", List.class, List.class, List.class, List.class);
    Type[] types = method.getGenericParameterTypes(); //按照方法参数声明顺序返回参数的 Type 数组
    for (Type type : types) {
        ParameterizedType parameterizedType = (ParameterizedType) type;
        Type[] actualTypeArguments = parameterizedType.getActualTypeArguments(); //返回表示此类型【实际类型参数】的 Type 数组
        if (actualTypeArguments[0] instanceof WildcardType) {
            WildcardType wildcardType = (WildcardType) actualTypeArguments[0];
            System.out.println("是通配符类型" + getTypeInfo(wildcardType));
            for (Type upperType : wildcardType.getUpperBounds()) {
                System.out.println("  upperType" + getTypeInfo(upperType));
            }
            for (Type lowerType : wildcardType.getLowerBounds()) {
                System.out.println("  lowerType" + getTypeInfo(lowerType));
            }
        } else {
            System.out.println("非通配符类型" + getTypeInfo(actualTypeArguments[0]));
        }
    }
}

public <T> void testType(List<T> a1, List<?> a2, List<? extends T> a3, List<? super Integer> a4) {
}
```

```txt
非通配符类型【T】    【TypeVariableImpl】    【TypeVariable,】
是通配符类型【?】    【WildcardTypeImpl】    【WildcardType,】
  upperType【java.lang.Object】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
是通配符类型【? extends T】    【WildcardTypeImpl】    【WildcardType,】
  upperType【T】    【TypeVariableImpl】    【TypeVariable,】
是通配符类型【? super java.lang.Integer】    【WildcardTypeImpl】    【WildcardType,】
  upperType【java.lang.Object】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
  lowerType【java.lang.Integer】    【Class】    【Serializable,GenericDeclaration,Type,AnnotatedElement,】
```
