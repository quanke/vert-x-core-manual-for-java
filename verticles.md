# Verticles

Vert.x 带有一个简单、 可扩展，actor-like 开箱即用，你可以用来节省您编写您自己的部署和并发模型。

此模型是完全可选的, 如果你不想去用，Vert.x 不会强制您以这种方式创建应用程序。

该模型并不要求是一个严格的[actor-model](http://my.oschina.net/quanke/blog/607173) 的实现，但它确实有相似之处，特别是对并发性，扩展和部署。

若要使用此模型，把代码设置为**verticles**.

Verticles 是代码的得到部署和运行的 Vert.x 块。Verticles 可以使用任何 Vert.x 支持的语言编写，单个应用程序包含 verticles， 可以使用多种语言编写。

你可能会想，verticle有点像 [Actor Model](http://en.wikipedia.org/wiki/Actor_model) 中的actor。

应用通常是同一个 Vert.x 实例，同时由多个verticle实例组成。不同的verticle实例通过[event bus](http://vertx.io/docs/vertx-core/java/#event_bus)发送消息。

