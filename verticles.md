# Verticles

Vert.x 带有一个简单、 可扩展，actor-like 开箱即用，你可以用来节省您编写您自己的部署和并发模型。

此模型是完全可选的, 如果你不想去用，Vert.x 不会强制您以这种方式创建应用程序。

该模型并不要求是一个严格的[actor-model](http://my.oschina.net/quanke/blog/607173) 的实现，但它确实有相似之处，特别是对并发性，扩展和部署。

若要使用此模型，把代码设置为**verticles**.

Verticles 是代码的得到部署和运行的 Vert.x 块。Verticles 可以使用任何 Vert.x 支持的语言编写，单个应用程序包含 verticles， 可以使用多种语言编写。

你可能会想，verticle有点像 [Actor Model](http://en.wikipedia.org/wiki/Actor_model) 中的actor。

应用通常是同一个 Vert.x 实例，同时由多个verticle实例组成。不同的verticle实例通过[event bus](http://vertx.io/docs/vertx-core/java/#event_bus)发送消息。




#### 

高可用性 (HA)可以在Verticles deployed时启动，当vert.x 的实例突然死了，从集群重新部署另外的vert.x 实例。

若要启用高可用性运行verticle，只是追加`-ha`开关:

```
vertx run my-verticle.js -ha
```

当启用高可用性，无需添加`-cluster`。

有关高可用性功能和配置的高可用性和故障转移（High Availability and Fail-Over）部分，有更多细节。

#### 从命令行运行 Verticles
使用 Vert.x ，通常可以直接在 Maven 或 Gradle 项目中添加 Vert.x core 库依赖。

还可以直接从命令行运行 Vert.x verticles。

做到这一点，你需要下载和安装一个 Vert.x ，并将安装的`bin`目录添加到`PATH`环境变量。还要确保`PATH`有 `Java 8 JDK`.

*注意!
JDK是需要支持的Java代码的即时编译。*

现在可以通过使用`vertx run`命令运行 verticles。这里有一些例子:

```
# Run a JavaScript verticle
vertx run my_verticle.js

# Run a Ruby verticle
vertx run a_n_other_verticle.rb

# Run a Groovy script verticle, clustered
vertx run FooVerticle.groovy -cluster
```

你甚至可以不编译直接运行Java源代码！

```
vertx run SomeJavaSourceFile.java
```

Vert.x 在运行之前会先编译Java源文件。这可以快速原型开发verticles，不需要设置 Maven 或 Gradle !

更多信息，请在命令行上执行`vertx`。

#### Vert.x 退出

由 Vert.x 实例维护的线程不是守护程序线程，从而防止 JVM 退出。

如果你嵌入 Vert.x 和你已完成了，你可以调用[close](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#close--)来关闭它。

这将关闭所有的内部线程池和关闭其他的资源，让 JVM 退出。

#### Context对象

当Vert.x提供一个事件的处理程序或调用[Verticle](http://vertx.io/docs/apidocs/io/vertx/core/Verticle.html)的开始或停止的方法，执行与`Context`相关联。一般Context是event-loop context 绑定特定的事件循环线程。因此，对于这方面的执行总是发生在该完全相同的事件循环线程。在worker verticles和运行内嵌阻止代码worker context的情况下将使用一个线程从worker线程池的执行关联。

若要获得context，请使用getOrCreateContext方法:

```
Context context = vertx.getOrCreateContext();
```

如果当前线程具有一个与它相关联的context，它重复使用context对象。如果不创建新实例的context。您可以测试您取得的context的类型:

```
Context context = vertx.getOrCreateContext();
if (context.isEventLoopContext()) {
  System.out.println("Context attached to Event Loop");
} else if (context.isWorkerContext()) {
  System.out.println("Context attached to Worker Thread");
} else if (context.isMultiThreadedWorkerContext()) {
  System.out.println("Context attached to Worker Thread - multi threaded worker");
} else if (! Context.isOnVertxThread()) {
  System.out.println("Context not attached to a thread managed by vert.x");
}
```

当您取得context对象时，您可以以异步方式在此context中运行代码。换句话说，您提交相同的context，但后来将最终运行任务:

```
vertx.getOrCreateContext().runOnContext( (v) -> {
  System.out.println("This will be executed asynchronously in the same context");
});
```

当几个处理程序在相同的context中运行时，他们可能想要分享数据。context对象提供方法来存储和获取在context中共享的数据。例如，它可以让你通过一些行动[runOnContext](http://vertx.io/docs/apidocs/io/vertx/core/Context.html#runOnContext-io.vertx.core.Handler-)运行数据：


```
final Context context = vertx.getOrCreateContext();
context.put("data", "hello");
context.runOnContext((v) -> {
  String hello = context.get("data");
});
```

context对象还可让您使用的config方法访问verticle配置。

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