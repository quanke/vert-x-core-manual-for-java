# Context对象
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
