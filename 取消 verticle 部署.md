# 取消 verticle 部署
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

