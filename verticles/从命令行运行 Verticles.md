# 从命令行运行 Verticles
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
