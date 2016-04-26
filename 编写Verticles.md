# 编写 Verticles
Verticle类必须实现[Verticle](http://vertx.io/docs/apidocs/io/vertx/core/Verticle.html)接口。

如果喜欢可以直接实现Verticle接口，但是通常简答的方法是继承抽象类[AbstractVerticle](http://vertx.io/docs/apidocs/io/vertx/core/AbstractVerticle.html)

下面是Verticles示例:

```
public class MyVerticle extends AbstractVerticle {

  // Called when verticle is deployed
  public void start() {
  }

  // Optional - called when verticle is undeployed
  public void stop() {
  }

}
```

通常你会像在上面的示例一样重写 start 方法。

当 Vert.x 部署verticle后，会调用 start 方法，当调用completed后，说明verticle启动完毕。

您也可以选择可以覆盖 stop 方法。取消部署的时候会调用。
