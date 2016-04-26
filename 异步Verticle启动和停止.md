# 异步Verticle启动和停止

实现异步启动

下面是一个示例:

```
public class MyVerticle extends AbstractVerticle {

  public void start(Future<Void> startFuture) {
    // Now deploy some other verticle:

    vertx.deployVerticle("com.foo.OtherVerticle", res -> {
      if (res.succeeded()) {
        startFuture.complete();
      } else {
        startFuture.fail();
      }
    });
  }
}
```

同样地，也是 stop 方法也是异步。如果你想要做一些verticle的清理工作，这需要一些时间，则如此使用。

```
public class MyVerticle extends AbstractVerticle {

  public void start() {
    // Do something
  }

  public void stop(Future<Void> stopFuture) {
    obj.doSomethingThatTakesTime(res -> {
      if (res.succeeded()) {
        stopFuture.complete();
      } else {
        stopFuture.fail();
      }
    });
  }
}
```

*信息: 你不需要在启动一个verticle后手动取消部署子 verticles，在verticle的 stop 方法。当父verticle’s取消部署时，Vert.x 将自动取消部署任何子 verticles。*