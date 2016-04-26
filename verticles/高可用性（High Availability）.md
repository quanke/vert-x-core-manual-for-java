# 高可用性（High Availability）
高可用性 (HA)可以在Verticles deployed时启动，当vert.x 的实例突然死了，从集群重新部署另外的vert.x 实例。

若要启用高可用性运行verticle，只是追加`-ha`开关:

```
vertx run my-verticle.js -ha
```

当启用高可用性，无需添加`-cluster`。

有关高可用性功能和配置的高可用性和故障转移（High Availability and Fail-Over）部分，有更多细节。
