# Verticles

Vert.x 带有一个简单、 可扩展，actor-like 开箱即用，你可以用来节省您编写您自己的部署和并发模型。

此模型是完全可选的, 如果你不想去用，Vert.x 不会强制您以这种方式创建应用程序。

该模型并不要求是一个严格的[actor-model](http://my.oschina.net/quanke/blog/607173) 的实现，但它确实有相似之处，特别是对并发性，扩展和部署。

若要使用此模型，把代码设置为**verticles**.

Verticles 是代码的得到部署和运行的 Vert.x 块。Verticles 可以使用任何 Vert.x 支持的语言编写，单个应用程序包含 verticles， 可以使用多种语言编写。

你可能会想，verticle有点像 [Actor Model](http://en.wikipedia.org/wiki/Actor_model) 中的actor。

应用通常是同一个 Vert.x 实例，同时由多个verticle实例组成。不同的verticle实例通过[event bus](http://vertx.io/docs/vertx-core/java/#event_bus)发送消息。



#### Verticle类型

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

#### 以编程方式部署 verticles

使用deployVerticle方法部署verticles，指定verticles名，也可以把已经创建的verticles实例传递过来。

*注意！
部署verticles实例只是 Java。*

```
Verticle myVerticle = new MyVerticle();
vertx.deployVerticle(myVerticle);
```

您还可以通过指定verticles名称部署 verticles.

verticles名称用于查找特定的[VerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/spi/VerticleFactory.html) ，将用来实例化实际verticles实例。

不同verticle工厂可用于实例化不同语言verticles和其他各种原因，例如装载服务和从Maven 在运行时得到verticles。

这允许您从 Vert.x 支持的任何语言编写的其他语言verticles部署 。

这里是 verticles 的部署一些不同类型的示例:

```
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle");

// Deploy a JavaScript verticle
vertx.deployVerticle("verticles/myverticle.js");

// Deploy a Ruby verticle verticle
vertx.deployVerticle("verticles/my_verticle.rb");
```

#### verticle名称映射到一个verticle工厂的规则

当部署 verticle(s) 使用一个名称，该名称用于选择将实例化 verticle(s) 的实际verticle工厂。

verticle的名称可以有前缀-它是一个字符串, 后跟一个冒号，它如果存在，则会被用来查找工厂，如

```
js:foo.js // Use the JavaScript verticle factory
groovy:com.mycompany.SomeGroovyCompiledVerticle // Use the Groovy verticle factory
service:com.mycompany:myorderservice // Uses the service verticle factory
```

如果没有前缀，则 Vert.x 将寻找一个后缀和使用，例如查找工厂，

```
foo.js // Will also use the JavaScript verticle factory
SomeScript.groovy // Will use the Groovy verticle factory
```

如果没有前缀或后缀，则 Vert.x 会假设它是 Java 完整类名称 (FQCN)，然后实例化。

#### 怎么样找到Verticle Factories?

大多数的Verticle factories在 Vert.x 启动时从类路径中加载并注册。

你可以通过编程方式注册和注销Verticle factories，使用[registerVerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#registerVerticleFactory-io.vertx.core.spi.VerticleFactory-)和[unregisterVerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#unregisterVerticleFactory-io.vertx.core.spi.VerticleFactory-)。

#### 等待部署完成

Verticle部署是异步的，可能部署完成后才返回。

如果你想要部署完成后通知，您可以部署指定完成处理程序:

```
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", res -> {
  if (res.succeeded()) {
    System.out.println("Deployment id is: " + res.result());
  } else {
    System.out.println("Deployment failed!");
  }
});
```

如果部署成功，该完成包含部署 ID 字符串的结果传递给处理程序。

如果你想要取消部署，可以稍后使用此部署 ID。

#### 取消 verticle 部署

部署可以通过[undeploy](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#undeploy-java.lang.String-)取消部署.

取消部署本身是异步的，所以如果想要在取消部署完成后通知，您可以部署指定完成处理程序:

```
vertx.undeploy(deploymentID, res -> {
  if (res.succeeded()) {
    System.out.println("Undeployed ok");
  } else {
    System.out.println("Undeploy failed!");
  }
});
```

#### 指定verticle实例数

在部署时verticle使用verticle的名称，可以指定您要部署的verticle实例的数目:

```
DeploymentOptions options = new DeploymentOptions().setInstances(16);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

用于跨多个内核轻松扩展。例如，您可能有 web 服务器verticle部署，服务器是多核的，你想要部署多个实例来充分利用所有核心。

#### 配置verticle

在部署的时可以传递一个JSON形式的配置给verticle：

```
JsonObject config = new JsonObject().put("name", "tim").put("directory", "/blah");
DeploymentOptions options = new DeploymentOptions().setConfig(config);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

这种配置可通过[Context](http://vertx.io/docs/apidocs/io/vertx/core/Context.html)对象获得或直接使用config方法。

作为一个 JSON 对象返回的配置，您可以检索数据，如下所示:

```
System.out.println("Configuration: " + config().getString("name"));
```

#### 在Verticle里访问环境变量。

使用 Java API 访问环境变量和系统属性:

```
System.getProperty("prop");
System.getenv("HOME");
```

#### Verticle隔离组

默认情况下，Vert.x 具有flat classpath。即，当 Vert.x 部署 verticles 使用当前类加载器-它不会创建一个新。在大多数情况下这是最简单、 最明确和理智的事情。

然而，在某些情况下，您可能想要部署verticle，所以在您的应用程序verticle的类是孤立于其他。

这可能并非如此，例如，如果您要部署两个不同版本的同一个 Vert.x 实例，类名相同的verticle或如果你有两个不同的 verticles，使用不同版本的相同的 jar 库。

隔离组，使用[setisolatedclasses](http://vertx.io/docs/apidocs/io/vertx/core/DeploymentOptions.html#setIsolatedClasses-java.util.List-) - 条目名称可以是完整类名，如`com.mycompany.myproject.engine.myclass`或它可以是一个通配符，将匹配任何包中的类和任何子的软件包，例如`com.mycompany.myproject.*`将匹配`com.mycompany.myproject`包中的任何类或任何子包。

请注意，只有匹配的将隔离 — — 任何其他类将由当前的类加载器加载。

也可以与setExtraClasspath提供附加的类路径条目，所以如果你想要加载的类或资源，已经不是目前的主要的类路径上你可以添加这。

如果你想加载尚不存在的主要类路径类或资源，你可以使用[setExtraClasspath](http://vertx.io/docs/apidocs/io/vertx/core/DeploymentOptions.html#setExtraClasspath-java.util.List-)

*警告!
谨慎使用此功能。类加载程序是一罐蠕虫，增加调试困难，除其他事项外。*

这里是一个使用隔离组隔离verticle deployment的示例。


```
DeploymentOptions options = new DeploymentOptions().setIsolationGroup("mygroup");
options.setIsolatedClasses(Arrays.asList("com.mycompany.myverticle.*",
                   "com.mycompany.somepkg.SomeClass", "org.somelibrary.*"));
vertx.deployVerticle("com.mycompany.myverticle.VerticleClass", options);
```

#### 高可用性（High Availability）

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