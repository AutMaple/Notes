# Jsp 的使用

## JSP 介绍

JSP ==> Java Server Pages ==> java服务器界面

工作流程：

- 服务器接收请求并找到对应的 jsp 页面之后，会将对应的 JSP 页面转换成 Java 文件。之后对转换的 Java 文件进行编译生成 class 文件， 之后由 class 问价提供访问支持

JSP 的本质： JSP 的本质是一个 Servlet。 可以前往 Tomact 中的 work 目录中查看 JSP 文件生成 class 文件

JSP 脚本：

1. `<% Code %>` ==> 定义的 Java 代码，在 JSP 生成 Java 代码之后会把 Code 放在 Servlet 的 Service 方法内
2. `<%! Code %>` ==> 定义的 Java 代码，在 JSP 生成 Java 代码之后会将其放在成员变量的位置
3. `<%= Code %>` ==> 定义的 Java 代码，会被绑定在 JSP 的 `out` 对象上。Code 中的代码输出什么，`out` 就可以输出什么。例如 `<%= var %>`, 最终显示给用户的就是变量 var 的值。相当于`System.out.println()`方法输出什么内容，那么最终就会在用户界面显示什么

## JSP 内置对象

| 内置对象    | 真实类型           | 作用                                         |
| ----------- | ------------------ | -------------------------------------------- |
| pageContext | PageContext        | 当前页面共享数据，可以获取其他的八个内置对象 |
| request     | HttpServletRequest | 一次请求访问你多个资源(转发)                 |
| response    | HttpServletRespone | 响应对象                                     |
| session     | HttpSession        | 一次会话多次请求                             |
| application | ServletContext     | 所用用户共享数据                             |
| page        | Object             | 当前页面( Servlet )的对象, this              |
| out         | JspWriter          | 输出对象，将数据输出到页面上                 |
| config      | ServletConfig      | Servlet 配置对象                             |
| exception   | Throwalble         | 异常对象                                     |

**注意**

- response 的输出优先与 out 对象的输出，response 的缓冲区数据永远优先与 out 对象的缓冲区数据输出。这是由 Tomcat 的实现决定的。Tomcat 会优先找 response 的缓冲区，然后再去找 out 的缓冲区。因此以后尽可能在不要使用 response 的输出流，统一使用 out 对象的输出流。

## JSP 指令

作用：用于配置 JSP 页面，导入资源

格式：`<%@ 指令名称 <key>=<value> ....<key>=<value> %>`

分类：

- `page`
  - contentType: 相当于 response.setContentType()
  - import: 用于导包
  - errorPage: 当页面发生异常之后，跳转到指定的页面
  - isErrorPage
    - true: 可以使用内置对象 exception
    - false: 默认值，不可以使用内置对象 exception
  - isELIgnored: 设置忽略 EL 表达式
- `include`: 包含模板文件
- `taglib`: 导入标签库
  - prefix: 标签前缀

## JSP 注释

- `<-- content -->`: 只能够注释 html 代码块
- `<%--content --%>`: 可以注释所有的代码块，包括 java 语句

建议使用 jsp 的注释

## EL表达式

EL ==> Expression Language

作用：替换和简化 jsp 页面中 java 代码的编写

语法：${ expression }

### 运算符

| 运算符                                                       |
| ------------------------------------------------------------ |
| `+`、`-`、`*`、 `/ (div)`、 `% (mod)`                        |
| `>`、 `>=`、 `< `、`<=`、` ==`、`!=`                         |
| `&&(and)`                                                    |
| `empty` ===> 判断引用类型为 null 或者 数组、字符串等长度为 0 |

### 获取值

EL 表达式只能够从域对象中获取值

语法：${ 域名.键名 } 从指定域中获取指定的键值, 也可以直接使用${ 键名 }, 它将从范围最小的域名中开始查找，直到找打对应的键名为止。本质是调用对象的 getter 方法。

域名称：

- pageScope ===> pageContext
- requestScope ===> request
- sessionScope ===> session
- applicationScope ===> application（ServletContext）

获取对象中的数据时需要提供对应的 getter 和 setter 方法，最好是一个 JavaBean 对象。

例如：${ user.name } 那么就会去调用 user.getName方法，然后将该方法的返回值输入到界面中。如果要将数据格式化，只需要在对象中设置 getter 方法，在方法中进行数据的处理，将处理后的数据作为返回值，然后给 EL 表达式使用

**注意**

上述的四个域对象，范围从小到大，可以进行数据共享。在进行数据共享时，能使用作用域小的域对象就使用作用域小的对象，如果共享的数据使用的作用于范围较大，容易造成数据权限的丢失。

## JSTL

JSTL ==> JavaServer Pages Tag Library JSP 标准标签库

作用： 用于简化和替换 JSP 页面上的 java 代码

使用步骤：

1. 导入 jstl 相关的 jar 包
2. 引入标签库： tablib 指令
3. 使用标签

### 常用的 JSTL 标签

- if： 相当于 java 中的 if 语句
- choose：相当于 java 中的 switch 语句
  - 使用choose 代替 switch
  - 使用 when 代替 case
  - 使用 otherwise 代替 default
- foreach相当于 java 中的 for 语句