# Object 对象

所有的类型都继承自 Object 对象，包括基本数据类型。该类中包含如下几个方法

| Methods                             | Description                                                  |
| :---------------------------------- | :----------------------------------------------------------- |
| **Equals(Object)**                  | Determines whether the specified object is equal to the current object. |
| **Equals(Object, Object)**          | Determines whether the specified object instances are considered equal. |
| **Finalize()**                      | Allows an object to try to free resources and perform other cleanup operations before it is reclaimed by garbage collection. |
| **GetHashCode()**                   | Serves as the default hash function.                         |
| **GetType()**                       | Gets the Type of the current instance.                       |
| **MemberwiseClone()**               | Creates a shallow copy of the current Object.                |
| **ReferenceEquals(Object, Object)** | Determines whether the specified Object instances are the same instance. |
| **ToString()**                      | Returns a string that represents the current object.         |

注意：

派生类可以重写的方法只有： `Equals`, `Finalize`, `GetHashCode` 和 `ToString`

# 变量的类型

C# 中变量分为两种类型：值类型和引用类型

其中值类型的变量存储的是真是的值；引用类型存储的是实例对象的引用

值类型有: 基本数据类型(byte, short, int, ....)，枚举类型，结构体类型，可空的类型(nullable value type)，以及元组类型(tuple value type)

引用类型：类，接口，数组，以及代理类型(delegate type)

# 类字段访问修饰符

- `public`: Access isn't limited.
- `private`: Access is limited to this class.
- `protected`: Access is limited to this class or classes derived from this class.
- `internal`: Access is limited to the current assembly (`.exe` or `.dll`).
- `protected internal`: Access is limited to this class, classes derived from this class, or classes within the same assembly.
- `private protected`: Access is limited to this class or classes derived from this type within the same assembly.

# override,virtual,abstract method

- A ***virtual*** method is one declared and implemented in a base class where any derived class may provide a more specific implementation.

- An ***override*** method is a method implemented in a derived class that modifies the behavior of the base class' implementation.
- An ***abstract*** method is a method declared in a base class that *must* be overridden in all derived classes. In fact, abstract methods don't define an implementation in the base class.

# Field And Property

```csharp
class Person
{
  private string name; // field

  public string Name   // property
  {
    get { return name; }   // get method
    set { name = value; }  // set method
  }
}
```

一个拥有公开的 set 和 get 方法的私有字段

可等价与如下：

```csharp
class Person
{
    public string Name {get;set;}
}
```

详细解释参考如下链接：[C# Properties (Get and Set) (w3schools.com)](https://www.w3schools.com/cs/cs_properties.php)

## Why Encapsulation(封装）?

- Better control of class members (reduce the possibility of yourself (or others) to mess up the code)
- Fields can be made **read-only** (if you only use the `get` method), or **write-only** (if you only use the `set` method)
- Flexible: the programmer can change one part of the code without affecting other parts
- Increased security of data

# Data Abstraction 数据抽象

Data **abstraction** is the process of hiding certain details and showing only essential information to the user.

# Why And When To Use Enums?

Use enums when you have values that you know aren't going to change, like month days, days, colors, deck of cards, etc.

# Delegate 委托

A [delegate](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types) is a type that represents references to methods with a particular parameter list and return type. When you instantiate a delegate, you can associate its instance with any method with a compatible signature and return type. You can invoke (or call) the method through the delegate instance.

delegate 类型代表一个拥有特定参数和返回值的方法引用。当我们实例化一个 delegate 时，可以将任何一个拥有兼容的签名和返回值的方法与这个实例进行关联，并且可以通过 delegate 实例调用关联的方法

**Event handlers are nothing more than methods that are invoked through delegates** 

事件处理程序只不过是通过 delegate 调用的方法

Any method from any accessible class or struct that matches the delegate type can be assigned to the delegate. The method can be either static or an instance method. This flexibility means you can programmatically change method calls, or plug new code into existing classes.

任何类和结构体中与 delegate 类型相匹配的方法都可以被赋值给 delegate。这些方法可以是静态方法也可以是实例方法。这种灵活性意味着我们可以在程序中改变方法的调用或者添加新的代码到已经存在的类中

> **In the context of method overloading, the signature of a method does not include the return value. But in the context of delegates, the signature does include the return value. In other words, a method must have the same return type as the delegate.**
>
> 在方法重载的情形中，方法的签名是不包括方法的返回值的，但是在 delegates 的情形中，方法的签名是包括函数的返回值。也就是说，在 delegate 中，除了参数要相同之外，返回值也必须相同，满足这两个条件的才可以被 delegate

## Delegate 的作用

Delegate 通常用于将函数作为另外一个函数的形参

1. 通过委托，可以将一个方法当作参数传递给另外一个方法，这对于回调函数非常有用
2. 委托的多播操作(+)可以让我们定义方法的执行顺序

## Delegates properties

- Delegates are similar to C++ function pointers, but delegates are fully object-oriented, and unlike C++ pointers to member functions, **delegates encapsulate both an object instance and a method**.
- Delegates allow methods to be passed as parameters.
- Delegates can be used to define callback methods.
- **Delegates can be chained together**; for example, multiple methods can be called on a single event.
- Methods don't have to match the delegate type exactly.返回值子类，参数为基类
- Lambda expressions are a more concise way of writing inline code blocks. **Lambda expressions (in certain contexts) are compiled to delegate types**.

# Event 事件

Event 是一个 特殊的 Delegate。

委托的方法其实就相当于订阅者，当 Event 手动触发时，就会订阅者(委托的方法）会自动执行对应的方法体

## What is the difference between delegate and event?

An **Event** declaration adds a layer of abstraction and protection on the **delegate** instance. This protection prevents clients of the delegate from resetting the delegate and its invocation list and only allows adding or removing targets from the invocation list.

参考链接：[https://stackoverflow.com/questions/29155/what-are-the-differences-between-delegates-and-events](https://stackoverflow.com/questions/29155/what-are-the-differences-between-delegates-and-events)

