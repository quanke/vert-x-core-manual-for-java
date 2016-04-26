# Verticle隔离组
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
