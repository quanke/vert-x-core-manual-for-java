# 指定verticle实例数

在部署时verticle使用verticle的名称，可以指定您要部署的verticle实例的数目:

```
DeploymentOptions options = new DeploymentOptions().setInstances(16);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

用于跨多个内核轻松扩展。例如，您可能有 web 服务器verticle部署，服务器是多核的，你想要部署多个实例来充分利用所有核心。