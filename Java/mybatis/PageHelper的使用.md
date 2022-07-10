# PageHelper 的使用

PageHelper 使用网址：[https://pagehelper.github.io/docs/howtouse/](https://pagehelper.github.io/docs/howtouse/)

PageHelper 的原理是通过实现 mybatis 的 interceptor 接口, 拦截 sql 并改造 sql，从而实现物理分页

## 分页插件的参数

- resonable: 该参数用于用户传入的页码数不合法时，自动查询第一页或者最后一页的数据。pageNum <= 0 时，返回第一页的数据，当 pageNum > maxPageNum 的时候返回最后一页的数据

## PageHelper 的注意事项

### PageHelper.startPage 方法重要提示

只有紧跟在 `PageHelper.startPage` 方法后的**第一个**Mybatis的**查询（Select）**方法会被分页。

### 请不要配置多个分页插件

请不要在系统中配置多个分页插件(使用Spring时,`mybatis-config.xml`和`Spring<bean>`配置方式，请选择其中一种，不要同时配置多个分页插件)！

### 分页插件不支持带有 `for update` 语句的分页

对于带有 `for update` 的sql，会抛出运行时异常，对于这样的 sql 建议手动分页，毕竟这样的 sql 需要重视。

### 分页插件不支持嵌套结果映射

由于嵌套结果方式会导致结果集被折叠，因此分页查询的结果在折叠后总数会减少，所以无法保证分页结果数量正确。