# 编写 HTTP 客户端

#### 创建 HTTP 客户端

使用默认选项创建一个HttpClient实例，如下所示:

```
HttpClient client = vertx.createHttpClient();
```

如果您想要在创建时配置客户端的选项，如下所示:

```
HttpClientOptions options = new HttpClientOptions().setKeepAlive(false);
HttpClient client = vertx.createHttpClient(options);
```
