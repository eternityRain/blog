---
title: Jsonschema2pojo
date: 2016-09-27 15:00:47
tags:
---

### 主流JSON库

在解决json2java的问题时，一般使用四种主流的JSON库来进行json解析：

-	JSON.simple
-	GSON
-	Jackson
-	JSONP

### Jsonschema2pojo

将JsonSchema翻译为pojo的工具 其底层使用的json2java工具即为Jackson 2.x Jsonschema2pojo定义了json schema的规范和书写方式

### Json Schema feature support

| JSON Schema rule                   | Supported | Since | Note                                                                        |
|------------------------------------|-----------|-------|-----------------------------------------------------------------------------|
| type (Simple)                      | Yes       | 0.1.0 |                                                                             |
| type (Union)                       | No        |       |                                                                             |
| properties                         | Yes       | 0.1.0 |                                                                             |
| patternProperties                  | No        |       |                                                                             |
| additionalProperties               | Yes       | 0.1.3 |                                                                             |
| items                              | Yes       | 0.1.0 |                                                                             |
| additionalItems                    | No        |       |                                                                             |
| required                           | Yes       | 0.1.6 |                                                                             |
| optional                           | Yes       | 0.1.0 | Deprecated                                                                  |
| dependencies                       | No        |       |                                                                             |
| minimum, maximum                   | Yes       | 0.3.2 | Via optional JSR-303 annotations                                            |
| exclusiveMinimum, exclusiveMaximum | No        |       |                                                                             |
| minItems, maxItems                 | Yes       | 0.3.2 | Via optional JSR-303 annotations                                            |
| uniqueItems                        | Yes       | 0.1.0 |                                                                             |
| pattern                            | Yes       | 0.3.2 | Via optional JSR-303 annotations                                            |
| minLength, maxLength               | Yes       | 0.3.4 | Via optional JSR-303 annotations                                            |
| enum                               | Yes       | 0.1.0 |                                                                             |
| default                            | Yes       | 0.1.7 |                                                                             |
| title                              | Yes       | 0.1.6 |                                                                             |
| description                        | Yes       | 0.1.0 |                                                                             |
| format                             | Yes       | 0.1.0 |                                                                             |
| divisibleBy                        | No        |       |                                                                             |
| disallow                           | No        |       |                                                                             |
| extends                            | Yes       | 0.1.8 |                                                                             |
| id                                 | No        |       |                                                                             |
| $ref                               | Yes       | 0.1.6 | Supports absolute, relative, slash & dot delimited fragment paths, self-ref |
| $schema                            | No        |       |                                                                             |

-	properties

 schema:

```
      {
            "type" : "object",
            "properties" : {
                "foo" : {
                    "type" : "string"
                }
            }
        }
```

 java:

```
    public class MyObject {
            private String foo;
            public String getFoo() {
               return foo;
            }
            public void setFoo(String foo) {
               this.foo = foo;
            }
        }
```

 properties中指定了schema对象的参数

-	type

 指定所定义的有类型概念的数据类型

| Schema                          | typeJava            | type |
|---------------------------------|---------------------|------|
| string                          | java.lang.String    |      |
| number                          | java.lang.Double    |      |
| integer                         | java.lang.Integer   |      |
| boolean                         | java.lang.Boolean   |      |
| object                          | generated Java type |      |
| array                           | java.util.List      |      |
| array (with "uniqueItems":true) | java.util.Set       |      |
| null                            | java.lang.Object\`  |      |
| any                             | java.lang.Object    |      |

-	additionalProperties

 schema

```
       {
            "type" : "object",
            "additionalProperties" : {}
        }
```

 java

```

        public class MyObject {

        private java.util.Map<String, Object> additionalProperties = new java.util.HashMap<String, Object>();

        @org.codehaus.jackson.annotate.JsonAnyGetter
        public java.util.Map<String, Object> getAdditionalProperties() {
            return this.additionalProperties;
        }

        @org.codehaus.jackson.annotate.JsonAnySetter
        public void setAdditionalProperties(String name, Object value) {
            this.additionalProperties.put(name, value);
        }

    }
```

 additionalProperties为true，会生成Map作为传入参数中附加参数的接受器，false则不生成，写成“{}”等同于true。 你也可以通过指定additionalProperties的数据类型来约束生成的结果 schema：

```
{
        "type" : "object",
        "additionalProperties" : {
            "type" : "number"
        }
    }
```

 java

```

        public class MyObject {

        private java.util.Map<String, Double> additionalProperties = new java.util.HashMap<String, Double>();

        @org.codehaus.jackson.annotate.JsonAnyGetter
        public java.util.Map<String, Double> getAdditionalProperties() {
            return this.additionalProperties;
        }

        @org.codehaus.jackson.annotate.JsonAnySetter
        public void setAdditionalProperties(String name, Double value) {
            this.additionalProperties.put(name, value);
        }

    }
```

 上面将additionalProperties的类型改为Double 如果将type指定为object（我的json文件名为source.json）

```
{
        "type" : "object",
        "additionalProperties" : {
            "type" : "object"
        }
    }
```

 java

```

	public class Source{

        private java.util.Map<String, SourceProperty> additionalProperties = new java.util.HashMap<String, SourceProperty>();

        @org.codehaus.jackson.annotate.JsonAnyGetter
        public java.util.Map<String, SourceProperty> getAdditionalProperties() {
            return this.additionalProperties;
        }

        @org.codehaus.jackson.annotate.JsonAnySetter
        public void setAdditionalProperties(String name, SourceProperty value) {
            this.additionalProperties.put(name, value);
        }

    }
```

 如上，会生成一个以主文件名为前缀，Property为后缀的类（SourceProperty）作为additionalProperties的value类型，该类中则有：

```
private Map<String, Object> additionalProperties = new HashMap<String, Object>();
```

-	array

 schema：

```
{
        "type" : "object",
        "properties" : {
            "myArrayProperty" : {
                "type" : "array",
                "items" : {
                    "type" : "string"
                }
            }
        }
    }
```

 array写法如上即会生成Array《string》类型的参数myArrayProperty，如果在myArrayProperty指定“uniqueItems”:true，生成的集合则为Set<>. 对于items，可以$ref引用其他jsonschema文件，也可以直接在其下编写jsonschema，会生成一个以集合参数为名称（MyArrayProperty）的对象作为该集合的泛型

-	enum

 schema:

```
 {
        "type" : "object",
        "properties" : {
            "myEnum" : {
                "type" : "string",
                "enum" : ["one", "secondOne", "3rd one"]
            }
        }
    }
```

 java:

```

	@Generated("com.googlecode.jsonschema2pojo")
    public static enum MyEnum {

    ONE("one"),
    SECOND_ONE("secondOne"),
    _3_RD_ONE("3rd one");
    private final String value;

    private MyEnum(String value) {
        this.value = value;
    }

    @JsonValue
    @Override
    public String toString() {
        return this.value;
    }

    @JsonCreator
    public static MyObject.MyEnum fromValue(String value) {
        for (MyObject.MyEnum c: MyObject.MyEnum.values()) {
            if (c.value.equals(value)) {
                return c;
            }
        }
        throw new IllegalArgumentException(value);
    }

	}

```

-	format

 可以给属性指定format规则，它会影响你的参数类型，和type对比，format的优先级更高，format的规则列表如下：

| Format value                        | Java type               |
|-------------------------------------|-------------------------|
| "date-time"                         | java.util.Date          |
| "date"                              | String                  |
| "time"                              | String                  |
| "utc-millisec"                      | long                    |
| "regex"                             | java.util.regex.Pattern |
| "color"                             | String                  |
| "style"                             | String                  |
| "phone"                             | String                  |
| "uri"                               | java.net.URI            |
| "email"                             | String                  |
| "ip-address"                        | String                  |
| "ipv6"                              | String                  |
| "host-name"                         | String                  |
| "uuid"                              | java.util.UUID          |
| anything else (unrecognised format) | type is unchanged       |

-	extends

 extends属性声明在schema层表明继承

 flower.json：

```
{
        "type" : "object"
    }
```

 rose.json

```
{
        "type" : "object",
        "extends" : {
            "$ref" : "flower.json"
        }
    }
```

 Rose.java：

```
public class Rose extends Flower {
        ....
    }
```

-	$ref

#### Supported protocols

> -	http://, https://
> -	file://
> -	classpath:, resource:, java: (all synonyms used to resolve schemas from the classpath)

#### $ref同样支持内部类型的引用

```
{
        "type" : "object",
        "properties" : {
            "child1" : {
                "type" : "string"
            },
            "child2" : {
               "$ref" : "#/properties/child1"
            }
        }
    }
```

 上面这种child2引用了child1的属性

```
{
       "description" : "Tree node",
       "type" : "object",
       "properties" : {
          "children" : {
             "type" : "array",
             "items" : {
                 "$ref" : "#"
             }
          }
       }
    }
```

 这种是集合类型泛型为本schema类型，类似于tree结构 也可以直接定位到集合的items属性作为类型

-	javaType

	```
	{
	        "javaType" : "com.other.package.CustomTypeName",
	        "type" : "object"
	    }
	```

 如此生成会创建一个CustomTypeName类，并将属性类型指定为该类 如果不加"type" : "object"，则不会生成对应类，但是属性类型依然为该类名，报错而已 当然，你也可以直接指定已有的封装类

-	javaEnumNames

 schema:

```
    {
        "type" : "object",
        "properties" : {
            "foo" : {
                "type" : "string",
                "enum" : ["H","L"],
                "javaEnumNames" : ["HIGH","LOW"]
            }
        }
    }
```

 java:

```
public enum Foo {
    HIGH("H"),
    LOW("L")
    ...
}
```

-	javaInterfaces

 schema:

```
    {
        "javaInterfaces" : ["java.io.Serializable", "Cloneable"],
        "type" : "object"
    }
```

 java:

```
public class FooBar implements Serializable, Cloneable
{
...
}
```

-	javaName

 该属性用于指定类或属性的生成名称

```
    {
      "type": "object",
      "properties": {
        "a": {
          "javaName": "b",
          "type": "string"
        }
      }
    }
```

 java:

```

	public class MyClass {
    @JsonProperty("a")
    private String b;

    @JsonProperty("a")
    public String getB() {
        return b;
    }

    @JsonProperty("a")
    public void setB(String b) {
        this.b = b;
    }
	}

```

 如果使用在主schema级别，可以直接指定对应的类名
