# Java API 版本的Vert.x Core 手册

- 欢迎关注http://quanke.name/
- 交流群：`231419585`
- 阅读地址：http://vertx.help/
- 下载地址：https://www.gitbook.com/book/quanke/vert-x-core-manual-for-java
- 本书源码地址：https://github.com/quanke/vert-x-core-manual-for-java


------
源码在[github](https://github.com/eclipse/vert.x)上

Vert.x Core提供的功能：

* 编写TCP客户端和服务器
* 编写 HTTP 客户端和服务器包括 Websocket 支持
* 事件总线(Event bus)
* 共享的数据-本地的map和分布式的map
* 定时和延时运行
* 部署和非部署 Verticles
* Sockets
* DNS 客户端
* 文件系统
* 高可用性
* 集群

Vert.x核心功能是相当简单的 — — 你不会找到数据库访问、 授权或高级别 web 功能等，这些东西你可以在哪里找到？在这里-，**Vert.x ext**(扩展)。

Vert.x core 非常小，非常轻量级。只是使用你想要的部分。也是完全可嵌入在您现有的应用程序 — — 不强迫你使用特殊方式架构您的应用程序，这样你可以方向使用 Vert.x。


您可以使用任何 Vert.x 支持的其他语言的核心。这有点小酷-我们不强迫你使用 Java API ，JavaScript 或者 Ruby等都没问题 — — 毕竟，不同的语言有不同的习惯和语法，迫使Ruby 开发人员使用 Java 的语法，这会很奇怪 (举个例子)。相反，我们自动生成以 Java Api 为核心，等效、地道的每种语言。

从现在起我们会使用 core 指 Vert.x core。

如果你使用 Maven 或 Gradle，需要增加以下依赖才能使用Vert.x Core API:

* Maven (在你的pom.xml中增加):

```
<dependency>
  <groupId>io.vertx</groupId>
  <artifactId>vertx-core</artifactId>
  <version>3.2.0</version>
</dependency>
```

* Gradle (在您的build.gradle文件增加):

```
compile io.vertx:vertx-core:3.2.0
```

下面让我们来讨论 `core` 的不同概念和功能。
