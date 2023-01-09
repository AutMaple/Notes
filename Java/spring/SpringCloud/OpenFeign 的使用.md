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
@FeignClient(name="user", configuration = FeignConfiguration.class)
public interface UserController{
}
```

通过自定义的配置类 DecoderConfiguration，可以配置自定义的解码器。

自定义的解码器需要实现 `feign.codec.Decoder` 接口。接口的实现逻辑可以参考官方的一个实现类 `org.springframework.cloud.openfeign.support.ResponseEntityDecoder`

Decoder 接口定义如下：

```java
public interface Decoder {

  Object decode(Response response, Type type) throws IOException, DecodeException, FeignException;
}
```

- response: 微服务的响应
- type: 接口的返回值类型

Open Feign 的源码中，通过装饰模式，来实现对远程调用的返回值进行解码操作。对于普通的 Feign 接口，解码时，方法的调用顺序是：

```text
OptionalDecoder -> ResponseEntityDecoder -> SpringDecoder
```

真正执行解码操作的是 SpringDecoder。为了自定义解码操作，我们需要在 `ResponseEntityDecoder` 和 `SpringDecoder` 中间加入我们自定义的解码器：

```java
@RequiredArgsConstructor
public class FeignDecoder implements Decoder {
	private final SpringDecoder decoder;


    @Override
    public Object decode(Response response, Type type) throws IOException, DecodeException, FeignException {
        if (type != Result.class) {
            Type newType = new ParameterizedType() {

                @Override
                public Type[] getActualTypeArguments() {
                    return new Type[]{type};
                }

                @Override
                public Type getRawType() {
                    return Result.class;
                }

                @Override
                public Type getOwnerType() {
                    return null;
                }
            };
            return this.decoder.decode(response, newType);
        }
        return this.decoder.decode(response, type);
    }
}
```

通过配置类注册 Decoder 

```java
@Configuration  
public class FeignConfiguration {  
    @Bean
    Decoder feignDecoder(ObjectFactory<HttpMessageConverters> converters,
                         ObjectProvider<HttpMessageConverterCustomizer> customizers) {
        return new OptionalDecoder(new ResponseEntityDecoder(new FeignDecoder(new SpringDecoder(converters, customizers))));
    }
}
```

注入到 Feign 接口中

```java
@FeignClient(name="user", configuration = FeignConfiguration.class)
public interface UserController{
}
```

这样，自定义的解码器就定义好了。

# 错误解码器

Open Feign 允许我们在远程接口报错时，自定义错误解码器。如果远程接口的返回的状态码是 `4XX` 或者是 `5XX` 并且对应的 Feign 接口没有配置熔断。那么 Open Feign 就会将错误交给错误解码器处理。

要自定义解码器，需要实现 `feign.codec.ErrorDecoder` 接口，接口的定义如下：

```java
public interface ErrorDecoder{
	Exception decode(String methodKey, Response response);
}
```

该接口需要我们返回一个 Exception 对象。