# JavaWeb基础

## Maven web 项目目录结构

```PLAINTEXT
src/
├──main
	├──java
		├──dao
		├──service
		├──entity
		├──utils
		├──web
	├──resources
	├──webapp
		├──css
		├──js
		├──font
		├──img
		├──WEB-INF
			├──web.xml
		├──index.jsp
├──test
	├──java
├──pom.xml
```

### 各目录的作用

- `java`: 存放 java 代码
- `dao`: `data access object`，数据访问对象，用于访问数据库中的数据，并将数据进行封装，供 service 包中的类进行调用
- `service`: 编写业务逻辑，将结果返回给 web 保重的类进行判断
- `utils`: 工具类
- `web`: 存放 Servlet
- `resources`: 存放配置文件
- `webapp`: 存放 jsp 等前端代码

## Tomcat 的使用

### Tomcat 的文件目录

```PLAINTEXT
apache-tomcat-10.0.7
├── bin
├── conf
├── lib
├── logs
├── temp
├── webapps
└── work
├── BUILDING.txt
├── CONTRIBUTING.md
├── LICENSE
├── README.md
├── NOTICE
├── RELEASE-NOTES
├── RUNNING.txt
```

各目录的作用

- `bin`: 存放可执行文件的目录，比如启动和关闭 Tomcat
- `conf`: 存放配置文件
- `lib`: 存放依赖的 jar 包
- `logs`: 存放日志文件
- `temp`: 存放临时文件
- `webapp`: 存放 web 项目, 编写好的 web 项目放在该目录下，供 Tomcat 使用

## 部署项目到 Tomcat 中

将项目部署到 Tomcat 中有三种方式

1. 直接将项目放在 webapp 目录下
2. 配置 `conf/server.xml` 文件
3. 在 `conf/Catalina/localhost` 创建 xml 文件进行部署

推荐使用第三种方式在 Tomcat 中部署项目

### 第一种方式 – 直接放在 webapp 目录下

比如要将一个名为 hello 的项目部署到 Tomcat 中，可以直接将 hello 这个文件夹放入 webapp 文件夹中，然后就可以通过虚拟目录 `/hello` 对项目 hello 进行访问

当在浏览器中输入`http://localhost/hello`时，Tomcat 会自动在 webapp 目录下寻找 hello 项目，并将 hello 项目中的 `index.html` 文件渲染到浏览器中进行显示

### 第二种方式 – 配置 conf/server.xml

可以对`conf/server.xml`文件进行相关的配置从而部署项目

在 `conf/server.xml`文件中的`<Host>`标签内进行配置

```XML
<Context docBase="<projectPath>", path="<url>"/>
```

- projectPath: 填写项目所在的路径
- path: 填写浏览器访问的虚拟路径

例如在 `/home/maple` 目录下有一个 hello 项目，docBase 就应该填写 `/home/maple/hello`, 而 path 属性则可以任意写，比如`/hehe`。 这项配置就是告诉 Tomcat 当访问`/hehe`路径时，去`/home/maple/hello`目录下寻找相关的配置，并进行显示

### 第三种方式 – 在 conf/Catalina/localhost 文件夹下创建配置文件

在 `conf/Catalina/localhost`目录下创建任意文件名的 xml 文件，并在其中添加配置

```XML
<Context docBase="<projectPath>", path="<url>"/>
```

各参数的作用和第二种方式相同

该方式中，可以将 path 这个属性删去，不写，此时如果要访问对应的目录，则虚拟路径则为该配置文件的文件名

例如在`conf/Catalina/localhost`目录下创建一个 `hehe.xml`, 并在其中配置

```XML
<Context docBase="/home/maple/hello>"/>
```

此时要访问 hello 这个项目就需要使用 `/hehe` 这个虚拟路径访问该项目下的文件

这种配置方式是一种热部署的方式，如果不想要部署该项目了，可以直接将对应的配置文件删除即可，而不需要重新启动 Tomcat

服务器。

## Servlet 的使用

Servlet 是一个接口，定义了 java 类被 Tomcat 识别的规则

如果想定义自己的规则供 Tomcat 识别和使用，需要实现 Servlet 接口

### 快速使用

1. 创建一个 web 项目
2. 定义类实现 Servlet 接口，并实现其中的方法
3. 在 web.xml 中配置 servlet

在 web.xml 中配置如下

```XML
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.autmaple.servlet.Hello</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
</web-app>
```

需要在配置文件中添加`servlet`和`servlet-mapping`这两个标签

其中`<servlet-class></servlet-class>`填写需要注册的 Servlet 的全称，之后便可以使用 `/hello` 访问相应的资源，并将请求交给`com.autmaple.servlet.Hello`处理

#### 执行原理

1. 当服务器接收客户端浏览器的请求后，会解析请求的 URL 路径，获取访问的 Servlet 的资源路径
2. 根据路径查找 web.xml 文件，是否有对应的 url-pattern 标签的内容
3. 如果有，则会找到对应的 的全类名
4. Tomcat 会将对应的类的字节码文件加载进内存中，并为其创建对象
5. 调用对象相应的方法

### Sevlent 定义的五个方法

```JAVA
// Servelte 创建时执行，只执行一次
public void init(ServletConfig servletConfig；
    
// 获取 Servlet 配置对象     
public ServletConfig getServletConfig();

// 用于提供服务的方法，可以执行多次,每次访问 Servlet 都会被调用
public void service(ServletRequest servletRequest, ServletResponse servletResponse);
                 
// 获取 Servlet 的配置信息，如版本等信息
public String getServletInfo();

// Servlet 被销毁时执行
public void destroy()
```

其中的`init`, `service`, `destroy` 方法代表着 Servlet 的周期

`init`方法在`Servlet`被创建是进行调用。可以在配置文件中设置什么时候调用`init`方法，只需要在配置文件对应的`Servlet`中设置`<load-on-startup>`属性即可,设置为正数或者 0，表示在启动 Tomcat 服务器时执行 init 方法；设置成负数时，表示在创建 Servlet 时调用 init 方法

示例代码

```XML
<servlet>
  <servlet-name>hello</servlet-name>
  <servlet-class>com.autmaple.servlet.Hello</servlet-class>
  <load-on-startup>5</load-on-startup>
</servlet>
```

### Servlet 体系结构

`Servlet` <== `GenericServlet` <== `HttpServlet`

- `GennriServlet`： 是一个抽象类，它实现了除`service`方法外的其他四个方法(空实现)，对于继承 GenericServlet 的类，只需要实现`service`方法即可
- `HttpServlet`： 对 HTTP 协议进行了封装，到时候后只需要实现 `doGet `和 `doPost `方法就行

### WebServlet 注解

使用`web.xml`这种配置文件的方式配置创建的而每一个 Servlet 非常的麻烦，没添加一个 Servlet 都需要去配置 `web.xml`问价，因此，在 Servlet 3.0 中推出了 `WebServlet`注解的方式来实现 Servlet 到虚拟地址的映射，非常的方便，使用的时候只需要在每一个 Servlet 类中添加一个`WebServlet`注解，并设置访问路径即可。示例代码如下

```JAVA
@WebServlet("/index")
public class Index extends HttpServlet {
    ......
}
```

这样设置之后，访问`/index`就会执行 `Index`这个 Servlet

### HttpServletRequest的使用

| 方法声明                     | 功能描述                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| String getMethod()           | 该方法用于获取 HTTP 请求消息中的请求方式（如 GET、POST 等）  |
| String getRequestURI()       | 该方法用于获取请求行中的资源名称部分即位于 URL 的主机和端门之后、参数部分之前的部分 |
| String getQueryString()      | 该方法用于获取请求行中的参数部分，也就是资源路径后问号（？）以后的所有内容 |
| String getContextPath()      | 该方法用于获取请求 URL 中属于 Web 应用程序的路径，这个路径以 / 开头，表示相对于整个 Web 站点的根目录，路径结尾不含 /。如果请求 URL 属于 Web 站点的根目录，那么返回结果为空字符串（””） |
| String getServletPath()      | 该方法用于获取 Servlet 的名称或 Servlet 所映射的路径         |
| String getRemoteAddr()       | 该方法用于获取请求客户端的 IP 地址，其格式类似于 192.168.0.3 |
| String getRemoteHost()       | 该方法用于获取请求客户端的完整主机名，其格式类似于 pcl.mengma.com。需要注意的是，如果无法解析出客户机的完整主机名，那么该方法将会返回客户端的 IP 地址 |
| int getRemotePort()          | 该方法用于获取请求客户端网络连接的端口号                     |
| String getLocaIAddr()        | 该方法用于获取 Web 服务器上接收当前请求网络连接的 IP 地址    |
| String getLocalName()        | 该方法用于获取 Web 服务器上接收当前网络连接 IP 所对应的主机名 |
| int getLocalPort()           | 该方法用于获取 Web 服务器上接收当前网络连接的端口号          |
| String getServerName()       | 该方法用于获取当前请求所指向的主机名，即 HTTP 请求消息中 Host 头字段所对应的主机名部分 |
| int gctServcrPort()          | 该方法用于获取当前请求所连接的服务器端口号，即 HTTP 请求消息中 Host 头字段所对应的端口号部分 |
| StringBuffer getRequestURL() | 该方法用于获取客户端发出请求时的完整 URL，包括协议、服务器名、端口号、 资源路径等信息，但不包括后面的査询参数部分。注意，getRequcstURL() 方法返冋的结果是 StringBuffer 类型，而不是 String 类型，这样更便于对结果进行修改 |
| BufferReader getReader()     | 该方法用于获取请求体中的数据(Post 请求中的参数部分，Get 请求的请求体为空，其参数放在地址部分） |

方法的使用参考自:[HttpServletRequest详解：获取HTTP请求消息](http://c.biancheng.net/view/4008.html)

#### URI 和 URL

`URL`: 统一资源定位符 => `https://blog.autmaple.com/tags/java`

`URI`: 统一资源标识符 => `/tags/java`

URL 表示的范围比 URI 小

#### 获取参数的通用方式

1. `String getParameter(String name)`

   根据`name`获取`name`对应的值

2. `String[] getParameterValues(String name)`

   根据`name` 获取对应的多个 `value`数组

3. `Enumeration<String> get ParameterNames()`

   获取所有请求的参数

4. `Map<String, String[]> get ParameterMap()`

   获取所有的参数 Map 集合

上述这几个方法，可以获取 Get 和 Post 请求的参数

#### 中文乱码问题

只需在获取参数前添加一句

```JAVA
req.setCharacterEncoding("utf-8");
```

就可以解决问题

#### 请求的转发

特点：

1. 将请求交给其他的 Servlet 处理，转发过程中，多个 Servlet 可以共享数据
2. 是一种服务器内部资源之间的跳转，不能够跳转到其他的服务器去请求资源
3. 在转发的过程中浏览器的地址不会发生变化，属于同一次请求

实现步骤：

1. 通过 Request 对象的 getRequestDispatcher(String path)获取 RequestDispatcher 对象
2. 通过 RequstDispatcher对象的 forward(ServletRequest req, ServletResponse resp) 方法进行跳转

示例代码

```JAVA
RequestDispatcher rd = req.getRequestDispatcher("/hello");
rd.forward(req,resp);
```

或者是直接一行搞定(推荐)

```JAVA
req.getRequestDispatcher("/hello").forward(req,resp);
```

#### 数据共享

方法

`void setAttribute(String name, Object obj)`: 存储数据

`Object getAttribute(String name)`: 通过`key` 获取 `value

`void removeAttribute(String name)`： 移除指定键值对

### HttpServletResponse的使用

#### 常用的方法

| 方法                                    | 作用                         |
| --------------------------------------- | ---------------------------- |
| `setContentType(String s)`              | 设置相应数据的格式和编码     |
| `sendRedirect(String url)`              | 设置重定向                   |
| `ServletOutputStream getOutPutString()` | 返回发送数据到客户端的输出流 |

#### 重定向

重定向：资源跳转的方式

代码实现

```JAVA
// 第一种方法
resp.setStatus(302);
resp.setHeader("locati共享这个对象on", "/index");

// 第二种方法
resp.sendRedirect("/index");
```

#### 重定向和转发的对比

重定向特点：

1. 地址蓝发生变化
2. 重定向可以访问其他服务器的资源
3. 重定向是两次请求，不能够使用 request 对象共享数据

转发特点：

1. 将请求交给其他的 Servlet 处理，转发过程中，多个 Servlet 可以共享数据
2. 是一种服务器内部资源之间的跳转，不能够跳转到其他的服务器去请求资源
3. 在转发的过程中浏览器的地址不会发生变化，属于同一次请求

#### 设置响应头让浏览器下载文件

如果想让浏览器下载某个文件，需要在响应头中设置 `content-disposition` 属性，值为 `attachment;filename=<filename>`，代码如下

```JAVA
resp.setHeader("content-disposition","attachment;filename=" + filename);
```

### ServletContext

介绍：Tomcat 服务器会为每一个项目工程( Web 应用)创建一个唯一的对象，这个对象就是 ServletContext 对象。该对象包含了项目下所有 Servlet 共享的资源信息。

作用：

1. 获取 MIME 类型
2. 分发请求
3. 记录日志

提供的方法：

```JAVA
public interface ServletContext {
    public String getContextPath();

   
    public String getMimeType(String file);
   

    public String getRealPath(String path);
    

    public Object getAttribute(String name);
    public Enumeration<String> getAttributeNames();
    public void setAttribute(String name, Object object);
    public void removeAttribute(String name);
    
    ....

}
```

#### getContextPath()

该方法用于获取 Web 应用的上下文的路径。例如将项目 hello 部署到 webapp 目录下，那么获取到的数据数据就是 `/hello`, 如果将 hello 部署到 ROOT 下，则获取到的数据为`""`。当然，这个路径可以在 server.xml 配置文件中进行修改。如果在配置文件中将 hello 项目的路径修改为 `/index`，那么获取到的上下文路径就是`/index`

#### getMimeTye()

**媒体类型**（通常称为 **Multipurpose Internet Mail Extensions** 或 **MIME** 类型 ）是一种标准，用来表示文档、文件或字节流的性质和格式。

格式：大类型/小类型，例如： `text/html`, `img/jpeg`。 在 Tomcat 的 `conf/web.xml` 中有各种类型的文件对应的 MIME.

该方法根据传入的文件，返回对应的 MIMI 字符串

#### getRealPath

getRealPath 方法用于获取资源的真实路径。其中 `/`表示项目的 Web 目录, 例如一个 Web 项目的目录结构如下

```PLAINTEXT
├── java
│   └── com
│       └── autmaple
├── resources
└── webapp
    ├── index.jsp
    └── WEB-INF
        └── web.xml
```

此时 `/` 表示的就是 `webapp` 目录，此时若要获取其中的 index.jsp ，就可以使用 `/index.jsp`,若要获取 web.xml， 可以使用 `/WEB-INF/web.xml`。 如果要获取 java 目录下的相关文件可以使用

```JAVA
<className>.class.getClassLoader().getResource("<source>");
```

也可以使用 getRealPath 方法，并传入 `/WEB-INF/classes/<类所在的位置>`，因为项目编写的 java 文件在编译之后都放在 WEB-INF/classes 目录下, 例如 java 目录下有一个 aaa.txt ， 那么传入的参数就是 `/WEB-INF/classes/aaa.txt`

## JavaBean

JavaBean： 标准的 Java 类

要求：

1. 类必须被 public 修饰
2. 必须提供空参的构造方法
3. 成员变量必须用 private 修饰
4. 提供公共的 getter 和 setter 方法

作用：封装对象

封装对象可以使用 BeanUtils 这个工具进行封装，这个工具需要自行下载

### BeanUtils 常用的方法

```JAVA
setProperty();
getProperty();
populate(Object obj, Map map); // 将 map 集合的键值对信息，封装到 JavaB
```

## 会话

http 连接是无状态的，也就是每次请求是独立的，这些请求间不能够共享数据

会话：一次会话中包含多次请求和响应。一次会话表示浏览器第一次给服务请发送请求，会话建立，直到有一方断开连接为止。

作用：在一次会话范围内的多次请求间，可以共享数据

方式：

- 客户端会话技术 => Cookie
- 服务端会话技术 => Session

### Cookie的使用

#### 获取Cookie

可以通过 HttpServletRequest 对象的 getCookie 方法进行获取，返回一个 Cookie 数组

```JAVA
public Cookie[] getCookie();
```

Cookie 中的键和值都是字符串类型，取出 Cookie 中的值通过 Cookie 的 `getName` 和 `getValue` 方法

```JAVA
public String getName();
public String getValue();
```

#### 发送Cookie

可以通过 HttpServletResponse 对象的 addCookie 方法设置 Cookie，该方法可以进行多次调用，从而实现在相应头中设置多个 Cookie

```JAVA
public void addCookie(Cookie cookie);
```

#### Cookie 的生命周期

1. 默认情况下，浏览器关闭，Cookie 失效
2. 持久化存储: 可以通过 Cookie 对象的 `setMaxAge` 方法设置存储的时间，单位是秒。默认值是负数，如果想删除浏览器中设置的 Cookie 可以将 `MaxAge`设置为 0 。

#### Cookie 中存储中文

1. 在 Tomcat 8 之前， cookie 中不能够直接存储中文数据。如果要在 Cookie 中存储中文数据，需要进行转码。转码一般使用 URL 编码。
2. 在 Tomcat 8 之后， 支持直接在 Cookie 中设置中文数据，不需要进行转码的操作。

#### Cookie 数据共享

1. 默认情况下，同一个项目中的 Cookie 不能够共享。如果要共享，可以使用 setPath 方法，并将值设置成 `/` 就能够实现共享。`/` 表示的是当前的虚拟目录

2. 不同的服务器中的 Cookie 默认也是不能共享的，如果需要共享，则可以使用 setDomain 方法，设置域名共享，从而实现不同服务器间数据的共享。例如

   ```JAVA
   setDomain(".baidu.com"); // 则 tieba.baidu.com 和 news.baidu.com 中的cookie 可以共享
   ```

#### cookie 的作用和特点

特点：

1. cookie 存储数据在浏览器中
2. 浏览器对单个 cookie 的大小有限制(一般限制在 4 kb)以及对同一个域名下的总 cookie 数量也是有限制的(一般限制在20个以内)。不同的浏览器，限制可能不一样。

作用:

1. cookie 一般用于存储一些不敏感的数据
2. 可以在不登录的情况下实现服务器对用户的识别

### Session 的使用

#### 获取 Session

使用 response 获取 Session 对象

```JAVA
response.getSession();
```

#### 常用方法

```JAVA
void setAttribution(String name, Object obj);
String getId();
Object getAtrribute(String s)
void invalidate();
```

#### 工作原理

Session 的实现依赖于 Cookie。在首次请求到来时，根据 Cookie 确定是否有 Session 存在，如果没有，则会创建一个 Session 对象， 并在响应中的 cookie 中设置一个 SesssionID并将该值设置为一个唯一值。当再次请求该网站给时，就会将对应的 SessionID 带上。

#### Session 的持久化存储

- Session 的钝化

  在服务器正常关闭之前，将 Session 对象序列化到硬盘上

- Session 的活化

  服务器启动后将 Session 文件转化为内存中的 Session 对象

Session 的钝化和活化，Tomcat 已经自动实现。只要服务器是正常关闭的，就能够正常的进行这两个操作

Tomcat 正常关闭指的是使用 Tomcat bin 目录下的 shutdown 脚本进行关闭。

#### Session 失效时间

默认的失效时间: 30 分钟。可以在 Tomcat 的 web.xml 中进行配置设置。web.xml 配置文件中有一个 `<session-config>`标签，可以在其中设置失效时间。也可以使用 Session 的 invalidate 方法让其失效

## Filter 过滤器

要使用过滤器需要实现 Filter 接口

### Filter 流程

过滤器流程：请求 => Filter => Servlet => Filter => 响应请求

请求经过过滤器交给 Servlet 处理，Servlet 处理之后，不会立即响应给客户端，需要再次经过过滤器之后才响应客户端的请求

### 使用 Filter

使用 Filter 有两种方式

1. 在 Filter 类上加上注解 `@WebFilter`

2. 在 web.xml 中进行配置

   ```XML
   <filter>
       <filter-name>demo1</filter-name>
       <filter-class>com.autmaple.filter.FilterDemo1</filter-class>
     </filter>
     <filter-mapping>
       <filter-name>demo1</filter-name>
       <!-- `/*` 表示的是拦截路径-->
       <url-pattern>/*</url-pattern>
     </filter-mapping>
   ```

### Filter 的生命周期

- `init`： 服务器启动后，调用 Filter 的 init 方法，该方法在生命周期内只会被调用一次
- `doFilter`: Filter 每拦截一次请求就会调用 doFilter 方法
- `destroy`:服务器正常关闭之前，调用 Filter 的 destroy 方法，该方法在生命周期内只能够被调用一次

### Filter 的配置

1. 拦截路径的配置

   - 具体资源路径: 例如 `/index.jsp` 只有访问 index.jsp 时，过滤器才会执行
   - 拦截目录: 例如 `/user/*` 访问 /user 下的所有资源都被拦截
   - 后缀名拦截：例如 `*.jsp` 访问所有后缀名为 jsp 的资源时，都会被拦截
   - 拦截所有资源: `/*` 表示拦截所有的请求

2. 拦截方式的配置

   - 注解配置(设置dispatcherTypes)

     1. REQUEST: 默认值，拦截浏览器发送的请求(Get, Post 等)
     2. FORWARD: 拦截转发的请求
     3. INCLUDE: 包含访问资源
     4. ERROR：错误跳转资源
     5. ASYNC: 拦截异步请求(如 AJXAX)

   - web.xml 配置

     直接使用`<dispatcher></dispatcher>`标签即可

3. 过滤器链(配置多个过滤器)

   - 执行顺序：过滤器1 ==> 过滤器2 ==> Servlet ==> 过滤器2 ==> 过滤器1

   - 过滤器执行的先后顺序：

     1. 注解配置：

        按照过滤器名称的字符串顺序执行，值小的过滤器先执行

        如：AFilter 和 BFilter, 则 AFilter 先执行

     2. web.xml 配置：

        谁先配置，谁先执行