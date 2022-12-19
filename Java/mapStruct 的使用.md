# 定义一个 mapper

map struct 中的 mapper，是一个被 `org.mapstruct.Mapper` 注解所修饰的一个接口：
```java
@Mapper
public interface CarMapper{

	@Mapping(target = "manufacturer", source = "make")
	@Mapping(target = "seatCount", source = "numberOfSeats")
	CarDto carToCarDto(Car car);

	@Mapping(target = "fullName", source = "name")
	PersonDto personToPersonDto(Person person);
}
```

@Mapper 注解会被 map struct 处理器所识别，并为改 Mapper 生成一个实现类，并且该实现类是在编译期生成的，而不是通过反射或者是其他手段在运行时动态生成，所以对性能没有很大的影响。

Mapper 的实现类会根据接口方法中标注的 @Mapping 注解中的信息，来生成对应的转换代码。

@Mapping 注解中的 target 是接口方法返回值类型的某个属性名，source 是接口方法参数类型中的某个属性名。该注解的作用就是告诉 map struct 的处理器将 source 属性的值赋值给 target 属性。如果 source 的属性名和 target 的属性名使相同的，则可以不使用 @Mapping 注解。

```ad-warning
title: 注意

source 属性必须有对应的 getter 和 setter 方法
```

如果只想映射 @Mapping 注解声明的字段，其他的字段不进行默认的映射，可以在接口方法上使用 `@BeanMapping(ignoreByDefault = true)`。

## 获取实现类的实例

```java
public interface DoctorMapper {
	DoctorMapper INSTANCE = Mappers.getMapper(DoctorMapper.class)

	DoctorDto doctorToDoctorDto(Doctor doctor);
}
```

通过 INSTANCE 就可以使用对应的方法:

```java
	DoctorMapper.INSTANCE.doctorToDoctorDto(doctor);
```

## 属性的自动类型转换

如果 @Mapping 中的 target 类型和 source 类型不一样时，map struct 可以做一些默认的转换。例如 source 中的类型是 int 类型，而 target 中类型是   String 类型。那么 map struct 在生成代码时，会调用 `String#valueOf(int)` 方法将 source 中的 int 类型转换成 target 的 String 类型。当然 map struct 也可以调用 `Integer#parseInt(String)` 方法将 String 类型转换成 Integer 类型。

map struct 默认支持的类型转换：

- 原始数据类型及其对应的包装类型
- 原始数字类型以及包装类型。例如可以将 byte 转换成 int， int 转换成 long 等。这种类型转化需要注意的就是精度丢失的问题，即：int 转换成 byte。我们可以通过配置 @Mapper 或者 @MapperConfig 注解的 `typeConversionPolicy` 属性来控制是否产生经过或者错误。该属性的默认值是 `ReportingPolicy.IGNORE`
- 所有原始数据类型到 String 类型之间的转换。并且在转换成 String 的过程中，可以通过 @Mapping 注解的 `numberFormat` 属性指定格式化字符串。这个格式化字符串最终会交给 `java.text.DecimalFormat` 格式化器进行格式化。

```java
@Mapper
public interface CarMapper{
	@Mapping(source = "price", numberFormat = "$#.00")
	CarDto carToCarDto(Car car);

	@IterableMapping(numberFormat = "$#.00")
	List<String> prices(List<Integer> prices);
}
```

- enum 和 String 类型的自动转换
- 大数据类型(BigInteger ...)、原始数据类型、包装类型以及 String 类型之间的转换。同样可以指定格式化字符串。
- 时间与 String 之间的转换，并且可以通过 @Mapping 注解的 `dateFormat` 字段设置格式化字符串。格式化字符串最终会被 `java.text.SimpleDateFormat` 处理。如果没有设置 `dateFormat`，则会使用默认的格式化字符串。
- String 与 StringBuilder 
- `java.net.URL` 与 String 

## 代码生成的逻辑

- 如果映射的类型相同，直接进行复制。基本数据类型赋值，List 等集合类型则进行复制
- 如果映射的类型不同，则会查看接口中有没有一个方法：参数类型与 source 的类型相同，返回值类型与 target 的类型相同。如果有，则会调用对应的方法。这种方式，可以实现进行任意深度的嵌套转换。
- 如果在接口中没有找到对应的转化方法，这是会使用 map struct 内置支持的类型转化方式进行转换

## 多个类转换成一个 DTO

map struct 同样支持将多个类转换成一个 DTO，只需要将多个类作为参数传递即可

```java
@Mapper
public interface DoctorMapper {
	@Mapping(source = "doctor.specialty", target = "specialization")
	@Mapping(source = "education.degreeName", target = "degree")
	DoctorDto doctorToDoctorDto(Doctor doctor, Education education);
}
```

如果多个类中包含多个相同的字段，此时必须明确的指明是哪一个类中的字段，否则会报错。

## 自定义实现类型转换方法


有时候 map struct 可能无法将一个类型转换成另一个类型，此时就需要我们自己手动编写一个方法来实现对应的逻辑，然后交给 map struct 进行使用。

map struct 提供了两种方式来实现：

1. java 8 的默认方法
2. 抽象类

### 默认方法实现

在 java 8+ 的版本中，可以通过默认方法来实现手动转换。map struct 在处理时，如果匹配，则会调用对应的默认方法。

例子：

```java
@Mapper
public interface CarMapper {
	@Mapping(...)
	   ...   
	CarDto carToCarDto(Car car);

	default PersonDto personToPersonDto(Person person) {
	   //hand-written mapping logic   
	}
}
```

### 抽象类实现

@Mapper 注解还可以放在抽象类上，从而实现手动实现部分类型的转换。抽象类相较于默认方法的好处是可以在抽象类中声明一些额外的字段。

通过抽象类的方式定义 Mapper， map struct 会生成该抽象类的子类，并实现对应的抽象方法。

例子：

```java
@Mapper
public abstract class CarMapper {
	@Mapper(...)
	...

	public abstract CarDto carToCarDto(Car car);

	public PersonDto personToPersonDto(Person person){
		// hand-write mapping logic	
	}
}
```

## 映射属性

假如现在有一个需求，需要将 Customer 类转换成 CustomerDto 对象。

Customer.java

```java
public class Customer {
	private Record record
	// other fields
}
```

Record.java

```java
public class Record {
	private String name;
	private String detail;
	// other fields
}
```

CustomerDto.java

```java
public class CustomerDto{
	private String name;
	private String detail;
	// other fileds
}
```

如果我们想将 Customer 类的 record 字段中的所有属性映射到 CustomerDto  类中而又不想要一个一个的写 @Mapping 注解的话，可以使用 `.` 来替代，示例如下：

```java
@Mapper
public interface CustomerMapper {
	@Mapping(source = "record", target = ".")
	CustomerDto toCustomerDto(Customer customer);
}
```

如果转换的方法有多个输入源(即方法有多个输入参数)，并且这些输入源中有字段名相同的属性时，需要使用 @Mapping 注解来明确的指出需要使用哪一个输入源对应的属性来消除这种歧义。比如

```java
@Mapping(souce = "record.name", target = "name")
@Mapping(source = "record", target = ".")
CustomerDto toCustomerDto(Customer customer);
```

## @MappingTarget

@MappingTarget 注解用于修饰方法中的参数，被该注解修饰的参数，会被其他传入的参数进行赋值操作。

当我们只是想修改实例的某个属性值，而不是创建一个新的实例时，可以使用 @MappingTarget 注解。

例如我们想要用 CarDto 的值来更新 Car，这可以使用该注解

```java
@Mapper
public interface CarMapper {
	void updateCarFromDto(CarDto dto, @MappingTarget Car car);
}
```

map struct 在实现 CarMapper 时，会使用 dto 的属性来更新 car 实例中对应的属性。

## 将 Map 映射成一个 Bean

map struct 支持将 Map<String, ?> 类型转换成一个 Bean。在这种情况下，@Mapping 注解中的 souce 属性则为 Map 中的 key。

```java
public class Customer {
	private Loing id;
	private String name;

	// getters, setters, ...
}
```

```java
@Mapper
public interface CustomerMapper {
	@Mapping(target = "name", souce = "customerName")
	Customer toCustomer(Map<String, String> map);
}
```

map struct 生成的代码：

```java
public class CustomerMapperImpl implements CustomerMapper {   
	@Override
	public Customer toCustomer(Map map) {
	  // ...
	  if ( map.containsKey( "id" ) ) {
	     customer.setId( Integer.parseInt( map.get( "id" ) ) ); 
	  } 
	  if ( map.containsKey( "customerName" ) ) {
	     customer.setName( map.get( "customerName" ) );
      }
         // ...
    }
}
```

## 为方法添加标识

有时候，在一个 mapper 里面可能有多个输入参数和输出参数相同的方法，但是功能不相同的情况，此时，如果没有为这些方法提供任何的标识的话，map struct 就不知道到底要调用哪一个方法，从而在编译时产生错误。

为此，map struct 提供 Qualifier 机制来标识，这种机制有两种实现机制：

1. 自定义注解，并且该注解上标有 @Qualifier 注解(`org.mapstruct.Qualifier`)，然后在对应的方法上使用自定义的注解
2. 在方法上使用 @Named 注解，从而给方法一个唯一的名字

### 自定义注解

```java
import org.mapstruct.Qualifier;

@Qualifier
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.CLASS)
public @interface TitleTranslator {

}
```

```java
import org.mapstruct.Qualifier;
@Qualifier
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
public @interface EnglishToGerman {
}
```

```java
import org.mapstruct.Qualifier;
@Qualifier
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
public @interface GermanToEnglish {
}
```

使用自定义的注解：

```java
@Mapper( uses = Titles.class )
public interface MovieMapper {
  @Mapping( target = "title", qualifiedBy = { TitleTranslator.class, EnglishToGerman.class } )
  GermanRelease toGerman( OriginalRelease movies );
}
```

```java
@TitleTranslator
public class Titles {
  @EnglishToGerman
  public String translateTitleEG(String title) {
	  // some mapping logic
  }
  @GermanToEnglish
  public String translateTitleGE(String title) {
	  // some mapping logic
  }
}
```

### 使用 @Named 注解

```java
@Named("TitleTranslator")
public class Titles {
  @Named("EnglishToGerman")
  public String translateTitleEG(String title) {
  // some mapping logic
  }
  @Named("GermanToEnglish")
  public String translateTitleGE(String title) {
  // some mapping logic
  }
}
```

```java
@Mapper( uses = Titles.class )
public interface MovieMapper {
  @Mapping( target = "title", qualifiedByName = { "TitleTranslator", "EnglishToGerman" } )
  GermanRelease toGerman( OriginalRelease movies );
}
```

## 映射枚举类型

映射枚举类型需要使用 @ValueMapping 注解

```java
@Mapper
public interface OrderMapper {
  OrderMapper INSTANCE = Mappers.getMapper( OrderMapper.class );
  @ValueMappings({
	  @ValueMapping(target = "SPECIAL", source = "EXTRA"),
	  @ValueMapping(target = "DEFAULT", source = "STANDARD"),
	  @ValueMapping(target = "DEFAULT", source = "NORMAL")
  })
  ExternalOrderType orderTypeToExternalOrderType(OrderType orderType);
}
```