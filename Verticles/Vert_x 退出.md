# Vert.x 退出

由 Vert.x 实例维护的线程不是守护程序线程，从而防止 JVM 退出。

如果你嵌入 Vert.x 和你已完成了，你可以调用[close](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#close--)来关闭它。

这将关闭所有的内部线程池和关闭其他的资源，让 JVM 退出。
