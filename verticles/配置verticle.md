# 配置verticle
在部署的时可以传递一个JSON形式的配置给verticle：

```
JsonObject config = new JsonObject().put("name", "tim").put("directory", "/blah");
DeploymentOptions options = new DeploymentOptions().setConfig(config);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

这种配置可通过[Context](http://vertx.io/docs/apidocs/io/vertx/core/Context.html)对象获得或直接使用config方法。

作为一个 JSON 对象返回的配置，您可以检索数据，如下所示:

```
System.out.println("Configuration: " + config().getString("name"));
```
