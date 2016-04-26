# 条目放入 JSON 对象


使用[put](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html#put-java.lang.String-java.lang.Enum-)方法将值放入的 JSON 对象。

该方法可以链接调用，因为使用 fluent API:

```
JsonObject object = new JsonObject();
object.put("foo", "bar").put("num", 123).put("mybool", true);
```
