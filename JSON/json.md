# JSON


不像一些其他语言，Java没有对JSON提供一流的支持，所以我们提供了两个类，来使你的应用程序Vert.x处理JSON更容易一点。

#### JSON 对象

[JsonObject](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html)类表示 JSON 对象。

JSON对象基本上是有字符串键和值的map，值可以是JSON的一个支持的类型（字符串，数字，布尔值）。

JSON 对象还支持 null 值。

##### 创建 JSON 对象

可以使用默认的构造函数创建空的 JSON 对象。

可以通过 JSON 格式字符串创建一个 JSON 对象，如下所示:

```
String jsonString = "{\"foo\":\"bar\"}";
JsonObject object = new JsonObject(jsonString);
```

##### 条目放入 JSON 对象

使用[put](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html#put-java.lang.String-java.lang.Enum-)方法将值放入的 JSON 对象。

该方法可以链接调用，因为使用 fluent API:

```
JsonObject object = new JsonObject();
object.put("foo", "bar").put("num", 123).put("mybool", true);
```

##### 从 JSON 对象中获取值

使用getXXX方法从 JSON 对象中获取值，例如 :

```
String val = jsonObject.getString("some-key");
int intVal = jsonObject.getInteger("some-other-key");
```

#####JSON 对象转换为字符串

使用 [encode](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html#encode--) 对象编码为一个字符串形式。

#### JSON 数组
[JsonArray](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonArray.html)类表示 JSON 数组。

一个 JSON 数组是一个序列的值 (字符串、 数字、 布尔值)。

JSON 数组也可以包含空值。

##### 创建 JSON 数组

可以使用默认的构造函数创建空的 JSON 数组。

可以通过 JSON 格式字符串创建一个 JSON 数组，如下所示:
```
String jsonString = "[\"foo\",\"bar\"]";
JsonArray array = new JsonArray(jsonString);
```

##### 添加一个条目到 JSON 数组

使用add方法将条目添加到 JSON 数组。

```
JsonArray array = new JsonArray();
array.add("foo").add(123).add(false);
```

##### 从 JSON 数组中获取值

使用getXXX方法从 JSON 数组中获取值，例如 :

```
String val = array.getString(0);
Integer intVal = array.getInteger(1);
Boolean boolVal = array.getBoolean(2);
```

##### JSON 数组转换为字符串

使用[encode](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonArray.html#encode--)方法将数组编码成字符串形式。
