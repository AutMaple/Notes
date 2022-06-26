# AOP 编程

AOP(Aspect Oriented Programming) 面向切面编程，通过预编译和运行期间动态代理的方式来实现程序功能的统一维护。利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使业务逻辑各部分之间的耦合度降低，提高程序的可重用性以及程序的开发效率

## AOP 相关的术语

### 通知 Advice

通知描述了切面要完成的工作以及何时执行。比如我们的日志切面需要记录每个接口调用时长，就需要在接口调用前后分别记录当前时间，再取差值。

- 前置通知 Before: 在目标方法调用前调用通知功能
- 后置通知 After: 在目标方法调用之后调用通知功能，不关心方法的返回结果
- 返回通知 AfterReturning: 在目标方法执行完毕之后调用通知功能
- 异常通知 AfterThrowing: 在目标方法抛出异常之后调用通知功能
- 环绕通知 Around: 在目标方法执行之前调用一次，目标方法执行完毕之后再调用一次

### 连接点 JoinPoint

通知功能被应用的时机。比如接口方法被调用的时候就是日志切面的连接点

### 切点 PointCut

切点定义了通知功能被应用的范围，比如日志切面的应用范围就是所有接口

### 切面 Aspect

切面是通知和切点的结合，定义了何时、何地应用通知功能

### 引入 Introduction

在无需修改现有列的情况下，向现有类添加新的方法或者属性

### 织入 Weaving

把切面应用到目标对象并创建新的代理对象的过程

## Spring 中使用注解创建切面

| 注解            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| @Aspect         | 用于定义切面                                                 |
| @Before         | 通知方法会在目标方法调用之前执行                             |
| @After          | 通知方法会在目标方法执行完毕或者抛出异常后执行               |
| @AfterReturning | 通知方法会在目标方法执行完毕之后执行                         |
| @AfterThrowing  | 通知方法会在目标方法抛出异常之后执行                         |
| @Around         | 通知方法在目标方法执行之前执行一次，目标方法执行完毕之后再执行一次 |
| @Pointcut       | 定义切点表达式                                               |

### 切点表达式

切点表达式指定了通知被应用的范围，表达式格式

```java
execution(方法修饰符 返回值类型 全限定包名.类名.方法名(方法参数))
```

execution 方法中的参数可以使用通配符进行修饰

如：

```java
//com.macro.mall.tiny.controller 包中所有类的 public 方法都应用切面里的通知
execution(public * com.macro.mall.tiny.controller.*.*(..))
//com.macro.mall.tiny.service 包及其子包下所有类中的所有方法都应用切面里的通知
execution(* com.macro.mall.tiny.service..*.*(..))
//com.macro.mall.tiny.service.PmsBrandService 类中的所有方法都应用切面里的通知
execution(* com.macro.mall.tiny.service.PmsBrandService.*(..))
```

