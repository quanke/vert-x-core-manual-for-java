# 以编程方式部署 verticles



使用deployVerticle方法部署verticles，指定verticles名，也可以把已经创建的verticles实例传递过来。

*注意！
部署verticles实例只是 Java。*

```
Verticle myVerticle = new MyVerticle();
vertx.deployVerticle(myVerticle);
```

您还可以通过指定verticles名称部署 verticles.

verticles名称用于查找特定的[VerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/spi/VerticleFactory.html) ，将用来实例化实际verticles实例。

不同verticle工厂可用于实例化不同语言verticles和其他各种原因，例如装载服务和从Maven 在运行时得到verticles。

这允许您从 Vert.x 支持的任何语言编写的其他语言verticles部署 。

这里是 verticles 的部署一些不同类型的示例:

```
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle");

// Deploy a JavaScript verticle
vertx.deployVerticle("verticles/myverticle.js");

// Deploy a Ruby verticle verticle
vertx.deployVerticle("verticles/my_verticle.rb");
```