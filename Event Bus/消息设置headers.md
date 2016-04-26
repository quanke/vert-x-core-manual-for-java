# 消息设置headers

通过event bus发送的消息还可以包含头文件。

这可以通过发送或发布时提供[DeliveryOptions](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/DeliveryOptions.html)指定:

```
DeliveryOptions options = new DeliveryOptions();
options.addHeader("some-header", "some-value");
eventBus.send("news.uk.sport", "Yay! Someone kicked a ball", options);
```