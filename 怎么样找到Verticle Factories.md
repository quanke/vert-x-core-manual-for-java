# 怎么样找到Verticle Factories?

大多数的Verticle factories在 Vert.x 启动时从类路径中加载并注册。

你可以通过编程方式注册和注销Verticle factories，使用[registerVerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#registerVerticleFactory-io.vertx.core.spi.VerticleFactory-)和[unregisterVerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#unregisterVerticleFactory-io.vertx.core.spi.VerticleFactory-)。