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






### 不要call（调用、打电话）我们，我们会call给你。

Vert.x Api 是很大程度上由事件驱动的。这意味着，当事情发生在你感兴趣的Vert.x，Vert.x 会通过回调方式向您发送events。

一些示例events:

* 计时器激活
* socket收到数据
* 从磁盘读取数据
* 发生了异常
* HTTP 服务器收到请求

通过向 Vert.x Api 提供处理程序来处理事件。例如要接收一个计时器事件每一秒你会做:

```
vertx.setPeriodic(1000, id -> {
  // This handler will get called every second
  System.out.println("timer fired!");
});
```

或接收到 HTTP 请求:

```
server.requestHandler(request -> {
  // This handler will be called every time an HTTP request is received at the server
  request.response().end("hello world!");
});
```

一段时间后当 Vert.x 有一个事件，它将传递到您的处理程序 Vert.x 将它异步调用.

这将引导我们进入Vert.x 中的一些重要概念:

### 不要阻塞我!

除了极少数例外 (一些文件系统操作的“同步”结束)，没有一个 Vert.x Api 阻塞调用线程。

如果可以立即提供的结果，它将立即返回，你通常会提供一个handle来接收过一段时间的事件。

由于Vert.x API没有任何阻塞的线程，这意味着你可以使用Vert.x来处理只是使用小数目线程的大量并发。

常规阻塞API使用线程可能会阻塞：

* 从socket读取数据
* 向磁盘写入数据
* 向收件人发送一条消息，等待答复。
* …

在所有上述情况下，当您的线程正在等待结果时它不能做别的-这是实际上是浪费。

这意味着，如果你需要大量的并发使用阻塞 APIs，然后你需要大量的线程，以防止您的应用程序停止工作。

线程在他们所需要的内存（例如栈）和上下文切换方面有开销。

对于许多现代应用程序所需要的并发水平，阻塞的方法不能按比例缩放。