# 不要call（调用、打电话）我们，我们会call给你。

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