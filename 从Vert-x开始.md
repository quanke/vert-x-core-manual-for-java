# 从Vert.x开始


*注意：这大部分是Java特有的-需要语言特有的调用方法*


如果没有获得[Vertx](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html)对象，Vert.x做不了什么。

Vertx对象是 Vert.x 的控制中心，几乎可以做所有事，包括创建客户端和服务器，获取引用到事件总线（event bus）、 设置计时器等。

所以怎么获得Vertx实例?

如果已经嵌入了 Vert.x，然后只需创建一个实例，如下所示:

```
Vertx vertx = Vertx.vertx();
```

如果使用 Verticles

*注意:大多数应用程序只需要一个单一的 Vert.x 实例，但如果你需要，可以创建多个 Vert.x 实例，例如，事件总线或不同的服务器和客户端之间的隔离。*

#### 创建一个指定选项的Vertx 对象

创建一个 Vertx 对象时，如果默认值不是正确的选择，你还可以指定选项:

```
Vertx vertx = Vertx.vertx(new VertxOptions().setWorkerPoolSize(40));
```

[VertxOptions](http://vertx.io/docs/apidocs/io/vertx/core/VertxOptions.html)对象有许多设置，可以配置集群、 高可用性、 池的大小等。所有设置细节在Javadoc 中有描述。

#### 创建群集 Vert.x 对象

如果您正在创建clustered（群集） Vert.x (更多集群相关的请参阅事件总线（ event bus）)，然后通常会使用异步方式创建 Vertx 对象。

这是因为不同的 Vert.x 实例在群集中组合在一起，通常需要一些时间 (也许几秒钟) 的。在这段时间，我们不想阻止调用线程，所以我们把结果以异步方式给你。