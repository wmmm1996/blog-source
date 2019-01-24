---
title: Jackson使用指南
date: 2019-01-21 20:12:32
tags:
  - jackson
categories: 
  - 工具使用
---

从事JAVA开发工作以来,一直都离不开Jackson的序列化反序列化,对于Jackson的使用也一直处于够用但不深入的状态，下面是日常使用过程中对Jackson的总结。

<!-- more -->

# Jackson常用注解

## 序列化注解

### @JsonAnyGetter
> 像普通属性一样序列化Map

```java
public class ExtendableBean {
    public String name;
    private Map<String, String> properties;
 
    @JsonAnyGetter
    public Map<String, String> getProperties() {
        return properties;
    }
}
```

序列化示例：

```json
{
    "name":"My bean",
    "attr2":"val2",
    "attr1":"val1"
}
```

### @JsonGetter
> 将指定的方法标记为`getter`方法。可以用来代替`@JsonProperty`

```java
public class MyBean {
    public int id;
    private String name;
 
    @JsonGetter("name")
    public String getTheName() {
        return name;
    }
}
```

序列化示例：

```json
{
    "id": 1,
    "name":"My bean"
}
```

### @JsonPropertyOrder
> 用在类上，在序列化的时候自定义属性输出顺序

```java
@JsonPropertyOrder({ "name", "id" })
public class MyBean {
    public int id;
    public String name;
}
```

序列化示例：

```json
{
    "name":"My bean",
    "id": 1
}
```

### @JsonRawValue
> 完全按照原样序列化属性的值

```java
public class RawBean {
    public String name;
 
    @JsonRawValue
    public String json;
}
```

例如：

```java
RawBean bean = new RawBean("My bean", "{\"attr\":false}");
```

将序列化为：

```json
{
    "name":"My bean",
    "json":{
        "attr":false
    }
}
```

而不是：

```json
{
    "name":"My bean",
    "json":"{\"attr\":false}"
}
```

### @JsonValue
> 定义整个实体的序列化方法，Jackson将会使用该方法的输出作为序列化输出。

```java
public enum TypeEnumWithValue {
    TYPE1(1, "Type A"), TYPE2(2, "Type 2");
 
    private Integer id;
    private String name;
 
    // standard constructors
 
    @JsonValue
    public String getName() {
        return name;
    }
}
```

序列化示例：

```json
{
  "name": "Type 2"
}
```

### @JsonRootName
> 如果需要将实体包装一层，可以使用`@JsonRootName`来指定根包装器的名称

```java
@JsonRootName(value = "user")
public class UserWithRoot {
    public int id;
    public String name;
}
```

序列化示例：

```json
{
    "user": {
        "id": 1,
        "name": "John"
    }
}
```

如果不用该注解，将会序列化为：

```json
{
    "id": 1,
    "name": "John"
}
```

### @JsonSerialize
> 用于指定自定义序列化器来序列化实体

```java
public class Event {
    public String name;
 
    @JsonSerialize(using = CustomDateSerializer.class)
    public Date eventDate;
}
```

自定义序列化器如下：

```java
public class CustomDateSerializer extends StdSerializer<Date> {
 
    private static SimpleDateFormat formatter 
      = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
 
    public CustomDateSerializer() { 
        this(null); 
    } 
 
    public CustomDateSerializer(Class<Date> t) {
        super(t); 
    }
 
    @Override
    public void serialize(
      Date value, JsonGenerator gen, SerializerProvider arg2) 
      throws IOException, JsonProcessingException {
        gen.writeString(formatter.format(value));
    }
}
```

输出示例：

```json
{
  "name": "test",
  "eventDate": "20-12-2014 02:30:00"
}
```

## 反序列化注解

### @JsonCreator
> 指定反序列化使用的构造函数或方法

待反序列化Json示例：

```json
{
    "id":1,
    "theName":"My bean"
}
```

```java
public class BeanWithCreator {
    public int id;
    public String name;
 
    @JsonCreator
    public BeanWithCreator(@JsonProperty("id") int id, @JsonProperty("theName") String name) {
        this.id = id;
        this.name = name;
    }
}
```

### @JacksonInject
> 指定某个字段从注入赋值，而不是从Json

```java
public class BeanWithInject {
    @JacksonInject
    public int id;
     
    public String name;
}
```

示例用法：

```java
String json = "{\"name\":\"My bean\"}";
 
InjectableValues inject = new InjectableValues.Std()
  .addValue(int.class, 1);
BeanWithInject bean = new ObjectMapper().reader(inject)
  .forType(BeanWithInject.class)
  .readValue(json);
```

### @JsonAnySetter
> 在反序列化时，将Map当成普通属性

待反序列化Json：

```json
{
    "name":"My bean",
    "attr2":"val2",
    "attr1":"val1"
}
```

```java
public class ExtendableBean {
    public String name;
    private Map<String, String> properties;
 
    @JsonAnySetter
    public void add(String key, String value) {
        properties.put(key, value);
    }
}
```

`properties`字段的值将会是由 `attr2 -> val2,attr1 -> val1`组成的键值对。

### @JsonSetter
> 将方法标记为`setter`方法，可以指定属性名称

```java
public class MyBean {
    public int id;
    private String name;
 
    @JsonSetter("name")
    public void setTheName(String name) {
        this.name = name;
    }
}
```

### @JsonDeserialize
> 用于指定自定义反序列化器来反序列化实体

```java
public class Event {
    public String name;
 
    @JsonDeserialize(using = CustomDateDeserializer.class)
    public Date eventDate;
}
```

对应的反序列化器：

```java
public class CustomDateDeserializer
  extends StdDeserializer<Date> {
 
    private static SimpleDateFormat formatter
      = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
 
    public CustomDateDeserializer() { 
        this(null); 
    } 
 
    public CustomDateDeserializer(Class<?> vc) { 
        super(vc); 
    }
 
    @Override
    public Date deserialize(
      JsonParser jsonparser, DeserializationContext context) 
      throws IOException {
         
        String date = jsonparser.getText();
        try {
            return formatter.parse(date);
        } catch (ParseException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## Jackson设置属性是否参与序列化

### @JsonIgnoreProperties
> 在类上指定要忽略的属性

```java
@JsonIgnoreProperties({ "id" })
public class BeanWithIgnore {
    public int id;
    public String name;
}
```

### @JsonIgnore
> 在具体属性上忽略，使其不参与序列化过程

```java
public class BeanWithIgnore {
    @JsonIgnore
    public int id;
 
    public String name;
}
```

与`@JsonIgnoreProperties`是等效的。

### @JsonIgnoreType
> 用在类上，将忽略该类所有属性

```java
public class User {
    public int id;
    public Name name;
 
    @JsonIgnoreType
    public static class Name {
        public String firstName;
        public String lastName;
    }
}
```

### @JsonInclude
> 用于排除值为`empty/null/default`的属性

```java
@JsonInclude(Include.NON_NULL)
public class MyBean {
    public int id;
    public String name;
}
```

### @JsonAutoDetect
> 强制序列化私有属性，不管它有没有`getter`方法

```java
@JsonAutoDetect(fieldVisibility = Visibility.ANY)
public class PrivateBean {
    private int id;
    private String name;
}
```

## Jackson处理多态

一般都是组合起来使用，有下面三个注解：

- @JsonTypeInfo
> 指定序列化中包含的类型信息的详细信息
- @JsonSubTypes
> 指定带注释类型的子类型
- @JsonTypeName
> 指定用于带注释的类的逻辑类型名称

```java
public class Zoo {
    public Animal animal;
 
    @JsonTypeInfo(
      use = JsonTypeInfo.Id.NAME, 
      include = As.PROPERTY, 
      property = "type")
    @JsonSubTypes({
        @JsonSubTypes.Type(value = Dog.class, name = "dog"),
        @JsonSubTypes.Type(value = Cat.class, name = "cat")
    })
    public static class Animal {
        public String name;
    }
 
    @JsonTypeName("dog")
    public static class Dog extends Animal {
        public double barkVolume;
    }
 
    @JsonTypeName("cat")
    public static class Cat extends Animal {
        boolean likesCream;
        public int lives;
    }
}
```

上述例子中，指定属性`type`为判断具体子类的依据，例如：`type=dog`，将被序列化为`Dog`类型。

## Jackson通用注解（序列化反序列化都生效）

### @JsonProperty
> 指定JSON中的属性名称

```java
public class MyBean {
    public int id;
    private String name;
 
    @JsonProperty("name")
    public void setTheName(String name) {
        this.name = name;
    }
 
    @JsonProperty("name")
    public String getTheName() {
        return name;
    }
}
```

### @JsonFormat
> 用于在序列化日期/时间值时指定格式。

```java
public class Event {
    public String name;
 
    @JsonFormat(
      shape = JsonFormat.Shape.STRING,
      pattern = "dd-MM-yyyy hh:mm:ss")
    public Date eventDate;
}
```

### @JsonUnwrapped
> 将对象中所有的属性与当前平级，不太好描述，简单说就是拆开包装。

```java
public class UnwrappedUser {
    public int id;
 
    @JsonUnwrapped
    public Name name;
 
    public static class Name {
        public String firstName;
        public String lastName;
    }
}
```

序列化示例：

```json
{
    "id":1,
    "firstName":"John",
    "lastName":"Doe"
}
```

如果不加`@JsonUnwrapped`注解，将被序列化为：

```json
{
    "id":1,
    "name": {
        "firstName":"John",
        "lastName":"Doe"
    }
}
```

### @JsonView
> 指定视图，类似分组进行序列化/反序列化

定义视图：

```java
public class Views {
    public static class Public {}
    public static class Internal extends Public {}
}
```

定义实体：

```java
public class Item {
    @JsonView(Views.Public.class)
    public int id;
 
    @JsonView(Views.Public.class)
    public String itemName;
 
    @JsonView(Views.Internal.class)
    public String ownerName;
}
```

序列化示例：

```java
String result = new ObjectMapper()
  .writerWithView(Views.Public.class)
  .writeValueAsString(item);
```

这时，将只会序列化`id`和`itemName`字段

### @JsonManagedReference, @JsonBackReference
> @JsonManagedReference和@JsonBackReference注释用于处理父/子关系并解决循环问题。

例如，有两个相互引用的类：

```java
public class ItemWithRef {
    public int id;
    public String itemName;
 
    @JsonManagedReference
    public UserWithRef owner;
}
```

```java
public class UserWithRef {
    public int id;
    public String name;
 
    @JsonBackReference
    public List<ItemWithRef> userItems;
}
```

不加注解，会循环调用，导致内存溢出，这时候可以使用`@JsonManagedReference`和`@JsonBackReference`来避免内存溢出。

### @JsonIdentityInfo
> 用于指定在序列化/反序列化值时使用对象标识，例如，处理无限递归类型的问题。

```java
@JsonIdentityInfo(
  generator = ObjectIdGenerators.PropertyGenerator.class,
  property = "id")
public class ItemWithIdentity {
    public int id;
    public String itemName;
    public UserWithIdentity owner;
}
```

### @JsonFilter
> 指定序列化期间要使用的过滤器。

```java
@JsonFilter("myFilter")
public class BeanWithFilter {
    public int id;
    public String name;
}
```

示例代码：

```java
BeanWithFilter bean = new BeanWithFilter(1, "My bean");

FilterProvider filters 
  = new SimpleFilterProvider().addFilter(
    "myFilter", 
    SimpleBeanPropertyFilter.filterOutAllExcept("name"));

String result = new ObjectMapper()
  .writer(filters)
  .writeValueAsString(bean);
```

## 自定义Jackson注解

可以使用@JacksonAnnotationsInside来开发自定义注解

```java
@Retention(RetentionPolicy.RUNTIME)
    @JacksonAnnotationsInside
    @JsonInclude(Include.NON_NULL)
    @JsonPropertyOrder({ "name", "id", "dateCreated" })
    public @interface CustomAnnotation {}
```

如何使用自定义注解：

```java
@CustomAnnotation
public class BeanWithCustomAnnotation {
    public int id;
    public String name;
    public Date dateCreated;
}
```

自定义注解可以**增强代码复用**，把一些通用的Jackson注解组合起来，形成一个新注解，新注解可以代替组合的注解。

## Jackson MixIn 注解
> 动态地为某些类型增加统一的Jackson注解

实体：

```java
public class Item {
    public int id;
    public String itemName;
    public User owner;
}
```

MixIn类：

```java
@JsonIgnoreType
public class MyMixInForIgnoreType {}
```

我们可以动态地让`User`类型不参与序列化：

```java
Item item = new Item(1, "book", null);
ObjectMapper mapper = new ObjectMapper();
mapper.addMixIn(User.class, MyMixInForIgnoreType.class);
result = mapper.writeValueAsString(item);
```

## 禁用Jackson注解

假设我们有一个带Jackson注解的实体：

```java
@JsonInclude(Include.NON_NULL)
@JsonPropertyOrder({ "name", "id" })
public class MyBean {
    public int id;
    public String name;
}
```

我们可以这样来禁用该实体上的所有Jackson注解：

```java
MyBean bean = new MyBean(1, null);
ObjectMapper mapper = new ObjectMapper();
mapper.disable(MapperFeature.USE_ANNOTATIONS);
```

# Jackson的`ObjectMapper`用法

## java类 转换为 json

可以直接序列化为Json字符串：

```java
objectMapper.writeValueAsString(car);
```

或者，可以序列化到文件，文件内容是Json字符串：

```java
objectMapper.writeValue(new File("target/car.json"), car);
```

## json 转换为 java类

从字符串：

```java
String json = "{ \"color\" : \"Black\", \"type\" : \"BMW\" }";
objectMapper.readValue(json, Car.class); 
```

从文件：

```java
objectMapper.readValue(new File("target/json_car.json"), Car.class);
```

从URL：

```java
objectMapper.readValue(new URL("target/json_car.json"), Car.class);
```

## json转换为Jackson JsonNode

```java
String json = "{ \"color\" : \"Black\", \"type\" : \"FIAT\" }";
JsonNode jsonNode = objectMapper.readTree(json);
String color = jsonNode.get("color").asText();
// Output: color -> Black
```

## json 转换为 java集合

```java
String jsonCarArray = 
  "[{ \"color\" : \"Black\", \"type\" : \"BMW\" }, { \"color\" : \"Red\", \"type\" : \"FIAT\" }]";
List<Car> listCar = objectMapper.readValue(jsonCarArray, new TypeReference<List<Car>>(){});
```

## json 转换为 Map

```java
String json = "{ \"color\" : \"Black\", \"type\" : \"BMW\" }";
Map<String, Object> map = objectMapper.readValue(json, new TypeReference<Map<String,Object>>(){});
```
## `ObjectMapper`的常用配置

忽略不识别的字段（json属性与目标实体存在属性上的差异）：

```java
objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
```

允许原始值为null：

```java
objectMapper.configure(DeserializationFeature.FAIL_ON_NULL_FOR_PRIMITIVES, false);
```

允许将枚举序列化/反序列化为数字：

```java
objectMapper.configure(DeserializationFeature.FAIL_ON_NUMBERS_FOR_ENUMS, false);
```

## 配置自定义序列化/反序列化器

假设有一个序列化器：

```java
public class CustomCarSerializer extends StdSerializer<Car> {
     
    public CustomCarSerializer() {
        this(null);
    }
 
    public CustomCarSerializer(Class<Car> t) {
        super(t);
    }
 
    @Override
    public void serialize(
      Car car, JsonGenerator jsonGenerator, SerializerProvider serializer) {
        jsonGenerator.writeStartObject();
        jsonGenerator.writeStringField("car_brand", car.getType());
        jsonGenerator.writeEndObject();
    }
}
```

一个反序列化器：

```java
public class CustomCarDeserializer extends StdDeserializer<Car> {
     
    public CustomCarDeserializer() {
        this(null);
    }
 
    public CustomCarDeserializer(Class<?> vc) {
        super(vc);
    }
 
    @Override
    public Car deserialize(JsonParser parser, DeserializationContext deserializer) {
        Car car = new Car();
        ObjectCodec codec = parser.getCodec();
        JsonNode node = codec.readTree(parser);
         
        // try catch block
        JsonNode colorNode = node.get("color");
        String color = colorNode.asText();
        car.setColor(color);
        return car;
    }
}
```

用`ObjectMapper`使用他们：

```java
//添加自定义序列化器
module.addSerializer(Car.class, new CustomCarSerializer());
//添加自定义反序列化器
module.addDeserializer(Car.class, new CustomCarDeserializer());
```

## 处理日期格式化

```java
ObjectMapper objectMapper = new ObjectMapper();
DateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm a z");
objectMapper.setDateFormat(df);
```

## 处理集合

反序列化为数组：

```java
String jsonCarArray = 
  "[{ \"color\" : \"Black\", \"type\" : \"BMW\" }, { \"color\" : \"Red\", \"type\" : \"FIAT\" }]";
ObjectMapper objectMapper = new ObjectMapper();
objectMapper.configure(DeserializationFeature.USE_JAVA_ARRAY_FOR_JSON_ARRAY, true);
Car[] cars = objectMapper.readValue(jsonCarArray, Car[].class);
```

反序列化为集合：

```java
String jsonCarArray = 
  "[{ \"color\" : \"Black\", \"type\" : \"BMW\" }, { \"color\" : \"Red\", \"type\" : \"FIAT\" }]";
ObjectMapper objectMapper = new ObjectMapper();
List<Car> listCar = objectMapper.readValue(jsonCarArray, new TypeReference<List<Car>>(){});
```

---
👉 [代码仓库](https://github.com/gcdd1993/Jackson-Guide-With-Samples)
👉 [Jackson JSON Tutorial](https://www.baeldung.com/jackson)