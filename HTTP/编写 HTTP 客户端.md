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


#### 发出请求

Http 客户端非常灵活，可以用各种方式请求。

http 客户端经常需要将许多请求发送到相同的主机/端口 。为了避免每次发出请求需要重复配置主机/端口，您可以配置客户端的默认主机/端口:

```
HttpClientOptions options = new HttpClientOptions().setDefaultHost("wibble.com");
// Can also set default port if you want...
HttpClient client = vertx.createHttpClient(options);
client.getNow("/some-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
});
```

或者可以指定单个请求的主机/端口。

```
HttpClient client = vertx.createHttpClient();

// Specify both port and host name
client.getNow(8080, "myserver.mycompany.com", "/some-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
});

// This time use the default port 80 but specify the host name
client.getNow("foo.othercompany.com", "/other-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
});
```

