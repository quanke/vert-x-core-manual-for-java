# Verticles

Vert.x 带有一个简单、 可扩展，actor-like 开箱即用，你可以用来节省您编写您自己的部署和并发模型。

此模型是完全可选的, 如果你不想去用，Vert.x 不会强制您以这种方式创建应用程序。

该模型并不要求是一个严格的[actor-model](http://my.oschina.net/quanke/blog/607173) 的实现，但它确实有相似之处，特别是对并发性，扩展和部署。

若要使用此模型，把代码设置为**verticles**.

Verticles 是代码的得到部署和运行的 Vert.x 块。Verticles 可以使用任何 Vert.x 支持的语言编写，单个应用程序包含 verticles， 可以使用多种语言编写。

你可能会想，verticle有点像 [Actor Model](http://en.wikipedia.org/wiki/Actor_model) 中的actor。

应用通常是同一个 Vert.x 实例，同时由多个verticle实例组成。不同的verticle实例通过[event bus](http://vertx.io/docs/vertx-core/java/#event_bus)发送消息。



#### 


#### 


#### 



#### 执行定期和延迟的操作

在Vert.x 中执行定期和延迟的操作是非常常见的。

在标准 verticles 中，不能使用thread sleep 引入延迟，这样会止事件循环线程。

相反，您可以使用 Vert.x 计时器。定时器可以一次性的计时器或定期的计时器。我们将讨论两个

##### 一次性的计时器




一个单次定时器有一定的延迟之后调用一个事件处理程序，以毫秒为单位表示。

使用[setTimeout](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#setTimer-long-io.vertx.core.Handler-)方法启动计时器，

```
long timerID = vertx.setTimer(1000, id -> {
  System.out.println("And one second later this is printed");
});

System.out.println("First this is printed");
```

返回值是一个唯一的定时器 id，以后可用于取消计时器。该处理器还通过定时器id。

##### 定期计时器

您还可以设置一个计时器来定期启动，通过使用[setPeriodic](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#setPeriodic-long-io.vertx.core.Handler-)方法。

会有一个初始延迟等于周期。

setPeriodic的返回值是一个唯一的计时器的 id (long)。如果以后计时器需要取消，可以使用id。

传递到计时器事件处理程序的参数也是唯一的计时器的 id:

请记住，计时器会定期触发。如果你的周期性处理需要相当长的时间进行，你的计时器事件可以运行连续或更糟的是: 堆积。


在这种情况下，您应该考虑使用[setTimer](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#setTimer-long-io.vertx.core.Handler-)替代。一旦您处理已完成，您可以设置下一个计时器。

```
long timerID = vertx.setPeriodic(1000, id -> {
  System.out.println("And every second this is printed");
});

System.out.println("First this is printed");
```

#### 取消计时器

若要取消一个定期的计时器，请调用cancelTimer指定的计时器的 id。例如:

```
vertx.cancelTimer(timerID);
```

#### Verticles 自动清理

如果您正在从 verticles 内创建的计时器，这些计时器将被自动关闭verticle undeployed。