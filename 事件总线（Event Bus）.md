# 事件总线（Event Bus）

[event bus](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html)是Vert.x 的中枢神经系统 。

通过Vert.x实例使用eventBus方法得到单一的event bus实例。
事件总线允许您的应用程序相互沟通，不论何种语言，他们写的以及他们是否在同一个 Vert.x 实例，或在一个不同的 Vert.x 实例的不同部分。

它甚至可以弥合，允许客户端 JavaScript 运行在浏览器上相同的事件总线进行通信。

事件总线构成了一个分布式对等消息传递系统跨越多个服务器节点和多个浏览器。

事件总线支持发布/订阅，点到点和请求-响应消息。

事件总线 API 是非常简单的。它基本上涉及注册处理程序，注销处理程序和发送和发布消息。

第一次一些理论: