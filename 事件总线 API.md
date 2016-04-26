# 事件总线 API

让我们跳进 API

##### 获取事件总线

你获取到事件总线的引用，如下所示:

```
EventBus eb = vertx.eventBus();
```

还有每个 Vert.x 实例事件总线的单个实例。

##### 注册处理程序

这个最简单的方法来注册一个处理程序用consumer。下面是一个示例:

```
EventBus eb = vertx.eventBus();

eb.consumer("news.uk.sport", message -> {
  System.out.println("I have received a message: " + message.body());
});
```

当邮件到达您的处理程序时，将调用您的处理程序，在message中传递.

从对 consumer() 的调用返回的对象是一个实例MessageConsumer
随后，此对象可以用于注销处理程序中，或使用处理程序以流的形式。

或者你可以使用consumer对返回 MessageConsumer 与没有处理程序设置，然后在那设置处理程序。例如:

```
EventBus eb = vertx.eventBus();

MessageConsumer<String> consumer = eb.consumer("news.uk.sport");
consumer.handler(message -> {
  System.out.println("I have received a message: " + message.body());
});
```

注册时聚集的事件总线上的一个处理程序，它可以有一些时间为要达到该群集的所有节点的登记。

如果你想要这已完成时通知，您可以注册的 MessageConsumer 对象上completion handler。

```
consumer.completionHandler(res -> {
  if (res.succeeded()) {
    System.out.println("The handler registration has reached all nodes");
  } else {
    System.out.println("Registration failed!");
  }
});
```

##### 未注册的处理程序

若要撤消注册的处理程序，调用unregister.

如果你在一个群集事件总线，未登记可能需要一些时间来传播跨节点，如果你想要这完成后通知使用unregister.

```
consumer.unregister(res -> {
  if (res.succeeded()) {
    System.out.println("The handler un-registration has reached all nodes");
  } else {
    System.out.println("Un-registration failed!");
  }
});
```
