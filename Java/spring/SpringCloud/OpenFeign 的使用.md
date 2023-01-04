# 自定义解码器

在我们通过 Feign 调用其他服务的接口时，返回的结果通常是一个被封装的类如 Result:

```java
public class Result<T> {
	private Integer code;
	private String message;
	private T data;
	// ....
}
```

如果是统一封装的类 Result，那么我们每次都需要在业务代码中使用如 `getData()` 的方法才能获取到这真实的业务数据，这是非常麻烦且蛋疼的。那么有没有一种方法能够将我们解救出来呢？答案是有的，那就是自定义解码器。

在使用 Open Feign 的时候，允许我们为 Feign 提供一个自定义的配置类，如：

```java
@FeignClient(name="user", configuration = DecoderConfiguration.class)
public interface UserController{
}
```

通过自定义的配置类 DecoderConfiguration，可以配置自定义的解码器。

自定义的解码器需要实现 `feign.codec.Decoder` 接口。接口的实现逻辑可以参考官方的一个实现类 `org.springframework.cloud.openfeign.support.ResponseEntityDecoder`

```java
public interface Decoder {

  Object decode(Response response, Type type) throws IOException, DecodeException, FeignException;
}
```

- response: 微服务的响应
- type:

