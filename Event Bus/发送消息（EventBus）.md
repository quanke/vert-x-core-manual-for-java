# 发送消息

发送一条消息将导致只有一个Handlers在接收该消息的地址注册。这是点对点的消息传递模式。这个Handlers的选择是一个非严格的循环方式。

发送一条消息时，可以[send](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html#send-java.lang.String-java.lang.Object-)

```
eventBus.send("news.uk.sport", "Yay! Someone kicked a ball");
```