# 编写 HTTP 客户端

#### 创建 HTTP 客户端
创建一个HttpClient实例使用默认选项，如下所示:

```
HttpClient client = vertx.createHttpClient();
```

如果您想要配置客户端的选项，创建它，如下所示:

```
HttpClientOptions options = new HttpClientOptions().setKeepAlive(false);
HttpClient client = vertx.createHttpClient(options);
```
