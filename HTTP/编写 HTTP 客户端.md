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

##### 没有请求body简单的请求

通常情况下，你会想要与没有请求body的 HTTP 请求。这通常是 HTTP GET，OPTIONS和HEAD请求。

这是 Vert.x http 客户端的最简单方法，使用前缀与Now的方法。例如[getNow](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpClient.html#getNow-int-java.lang.String-java.lang.String-io.vertx.core.Handler-)。

这些方法创建HTTP请求，并在单个方法调用发送，让你提供一个处理程序，将与HTTP响应时调用它回来。

```
HttpClient client = vertx.createHttpClient();

// Send a GET request
client.getNow("/some-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
});

// Send a GET request
client.headNow("/other-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
});
```


##### 编写普通的requests

不知道想要发送请求方法，运行时才知道。这个用例我们提供通用请求方法例如[request](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpClient.html#request-io.vertx.core.http.HttpMethod-int-java.lang.String-java.lang.String-)，可以在运行时指定 HTTP 方法:

```
HttpClient client = vertx.createHttpClient();

client.request(HttpMethod.GET, "some-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
}).end();

client.request(HttpMethod.POST, "foo-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
}).end("some-data");
```

##### 编写请求主体（bodies）

有时想要在requests里含有body，或者想写请求的headers。

可以使用[post](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpClient.html#post-int-java.lang.String-java.lang.String-)方法，或者普通的[request](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpClient.html#request-io.vertx.core.http.HttpMethod-int-java.lang.String-java.lang.String-)方法

这些方法发送的请求不会立即返回，而是返回[HttpClientRequest](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpClientRequest.html)实例，用于写入body和header。

下面是一些写入body和header的 POST 请求的例子: 

```
HttpClient client = vertx.createHttpClient();

HttpClientRequest request = client.post("some-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
});

// Now do stuff with the request
request.putHeader("content-length", "1000");
request.putHeader("content-type", "text/plain");
request.write(body);

// Make sure the request is ended when you're done with it
request.end();

// Or fluently:

client.post("some-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
}).putHeader("content-length", "1000").putHeader("content-type", "text/plain").write(body).end();

// Or event more simply:

client.post("some-uri", response -> {
  System.out.println("Received response with status code " + response.statusCode());
}).putHeader("content-type", "text/plain").end(body);
```

默认utf-8编码：

```
request.write("some data");

// Write string encoded in specific encoding
request.write("some other data", "UTF-16");

// Write a buffer
Buffer buffer = Buffer.buffer();
buffer.appendInt(123).appendLong(245l);
request.write(buffer);
```

如果你的 HTTP 请求只写入单个字符串或缓冲区，你可以写它和结束end函数单一调用中的请求。

```
request.end("some simple data");

// Write buffer and end the request (send it) in a single call
Buffer buffer = Buffer.buffer().appendDouble(12.34d).appendLong(432l);
request.end(buffer);
```

当你写到一个请求时，write第一次调用会导致写入网络的请求标头。

实际的写操作是异步的之前一段时间后调用已返回, 不是可能发生。

非分块请求正文与 HTTP 请求需要要提供的Content-Length标头。

因此，如果您不使用分块的 HTTP 然后之前，必须设置Content-Length标头写入请求，否则它将太迟了。

如果您正在调用接受字符串或缓冲区的end方法之一然后 Vert.x 将自动计算并设置Content-Length标头在写请求正文之前。

如果您使用的 HTTP 分块Content-Length标头不是必需的所以你不需要计算的前期的大小。