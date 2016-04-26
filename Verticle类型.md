# Verticle类型


有三种不同类型的 verticles:

##### 标准 Verticles
这些都是最常见和最有用的类型 — — 他们总是使用事件循环线程执行。更多讨论在下一节。

##### Worker Verticles
一个实例是永远不会有多个线程并发执行。

##### 多线程的worker verticles
一个实例可以由多个线程同时执行。

#### 标准 verticles
标准verticles当创建和调用start方法时分配一个event loop。调用执行都在相同的event loop上。

这意味着我们可以保证您的verticles实例中的所有代码总是都执行相同的事件循环上 (只要你不调用它自己创建的线程!)。

这意味着可以在程序里作为单线程编写所有的代码，把担心线程和扩展的问题交给Vert.x。没有更多令人担忧的同步和更多不稳定的问题，也避免了多线程死锁的问题。


#### Worker verticles

Worker verticles就像标准的verticles一样，但不使用事件循环执行，从 Vert.x worker线程池使用一个线程。

worker verticles 专为调用阻塞的代码，因为他们不会阻止任何事件循环。

如果你不想使用worker verticles运行阻塞的代码，可以在事件循环上直接运行内联阻塞代码。

如果您要以worker verticles的方式部署verticle，需要调用 [setWorker](http://vertx.io/docs/apidocs/io/vertx/core/DeploymentOptions.html#setWorker-boolean-).

```
DeploymentOptions options = new DeploymentOptions().setWorker(true);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

Worker verticle实例永远不会有多个线程并发执行 ，但可以在不同的时间由不同的线程执行。

##### 多线程Worker verticles

多线程的worker verticle就像正常worker verticle，但它是可以由不同的线程同时执行。

*警告！
多线程的worker verticle 是一项高级的功能，大多数应用程序会对他们来说没有必要。因为在这些 verticles 并发，你必须非常小心，使用标准的 Java 技术的多线程编程，以保持verticle一致状态。*