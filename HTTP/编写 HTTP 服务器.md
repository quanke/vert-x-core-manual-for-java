# 编写 HTTP 服务器

#### 创建一个 HTTP 服务器

最简单的方法来创建一个 HTTP 服务器，所有选项使用默认的。如下所示:

```
HttpServer server = vertx.createHttpServer();
```

#### 配置 HTTP 服务器

如果你不想使用默认值，创建服务器时可以通过传入一个[HttpServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerOptions.html)实例配置:

```
HttpServerOptions options = new HttpServerOptions().setMaxWebsocketFrameSize(1000000);

HttpServer server = vertx.createHttpServer(options);
```

#### 启动服务器监听

使用[listen](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServer.html#listen--)告诉服务器以监听传入的请求。

在选项中指定的主机和端口：

```
HttpServer server = vertx.createHttpServer();
server.listen();
```

或在调用listen中指定的主机和端口，忽略配置选项:

```
HttpServer server = vertx.createHttpServer();
server.listen(8080, "myhost.com");
```

默认主机是`0.0.0.0`， '监听所有可用的地址' ，默认端口是80.

实际的绑定是异步的，所以服务器可能不会实际被监听，直到一段时间后，调用返回。

如果想要listen实际监听后通知你，可以提供listen调用处理程序。例如:

```
HttpServer server = vertx.createHttpServer();
server.listen(8080, "myhost.com", res -> {
  if (res.succeeded()) {
    System.out.println("Server is now listening!");
  } else {
    System.out.println("Failed to bind!");
  }
});
```

#### 收到传入请求通知

若要请求到达时通知，需要设置[requestHandler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServer.html#requestHandler-io.vertx.core.Handler-):

```
HttpServer server = vertx.createHttpServer();
server.requestHandler(request -> {
  // Handle the request in here
});
```

#### 处理请求

当请求到达时，则该请求调用处理程序传递[HttpServerRequest](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html)的一个实例。此对象所表示的服务器端的 HTTP 请求。

当请求headers已完全读取时，将调用该处理程序。

如果该请求包含一个body，该body将到达服务器，一段时间后请求处理程序被调用。

服务器请求对象可以获取[uri](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#uri--)、[path](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#path--)、 [params](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#params--)和[headers](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#headers--)等。

每个服务器请求对象是与一台服务器的响应对象相关联。使用[response](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#response--)来获取对[HttpServerResponse](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html)对象。

这里是一个简单的例子，服务器处理请求和回复"hello world"。

```
vertx.createHttpServer().requestHandler(request -> {
  request.response().end("Hello world");
}).listen(8080);
```

##### 请求的版本

HTTP 请求中指定的版本，可以用[version](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#version--)获取

##### 请求方法

[method](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#method--)用于获得请求的 `HTTP` 方法 。(即是GET，POST、 PUT、 DELETE、 HEAD、OPTIONS等)。

##### 请求的 URI

使用[uri](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#uri--)来获取请求的 URI。

注意，这是通过在 HTTP 请求中，实际 URI，它几乎总是相对 URI。

URI是在 [HTTP规范的5.1.2节中定义 - 请求URI](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)

##### 请求路径

使用[path](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#path--)返回 URI 的路径

例如，如果请求 URI 只是:

`a/b/c/page.html?param1=abc&param2=xyz`

那么，路径会

`/a/b/c/page.html`

##### 请求查询

使用[query](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#query--)返回的 URI 的查询部分

例如，如果请求 URI 只是:

`a/b/c/page.html?param1=abc&param2=xyz`

那么，该查询会

`param1=abc&param2=xyz`

##### 请求headers

使用[headers](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#headers--)方法来返回的 HTTP 请求headers。

这将返回一个实例[MultiMap](http://vertx.io/docs/apidocs/io/vertx/core/MultiMap.html)-就像一个普通的map或Hash，但允许多个值的同一键-这是因为 HTTP 允许多个header值用相同的密钥。

`key` 不区分大小写，这就意味着您可以执行以下操作:

```
MultiMap headers = request.headers();

// Get the User-Agent:
System.out.println("User agent is " + headers.get("user-agent"));

// You can also do this and get the same result:
System.out.println("User agent is " + headers.get("User-Agent"));
```

##### 请求参数

使用[params](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#params--)返回的 HTTP 请求参数。

就像[headers](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#headers--)这返回的[MultiMap](http://vertx.io/docs/apidocs/io/vertx/core/MultiMap.html)实例，可以有多个参数具有相同的名称。

路径后的请求 URI是请求参数。例如，如果 URI:

```
/page.html?param1=abc&param2=xyz
```

然后参数将包含以下内容:

```
param1: 'abc'
param2: 'xyz
```

请注意这些请求参数从请求的 URL。如果您有已作为体内的multi-part/form-data请求提交 HTML 表单提交的一部分发送的窗体属性然后他们将不出现在这里的 params。

请注意，这些请求参数从请求的URL中获取。如果你的form属性为`multi-part/form-data`请求的话，参数不会出现在这里，会包含在body中提交发送。


##### 远程地址

请求的发送者的地址可以通过[remoteaddress](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#remoteAddress--)获取。

##### 绝对 URI

传入的 HTTP 请求的 URI 是通常相对。如果想要检索对应于该请求的绝对 URI，可以用[absoluteURI](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#absoluteURI--)

##### 结束处理程序

当整个，包括任何body已完全读取请求时，将调用[endHandler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#endHandler-io.vertx.core.Handler-)请求。

##### 从请求正body读取数据

通常一个 HTTP 请求包含我们想要读取的body。前面所提到的请求处理程序仅仅有headers，这里并没有body。

这是因为body可能会非常大 (例如一个文件上传)，我们通常不不会吧缓冲的整个body放在内存中交给你，因为那将导致服务器内存用尽。

若要接收body，您可以使用[handler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#handler-io.vertx.core.Handler-)请求，调用后，会有请求body到达。下面是一个示例:

```
request.handler(buffer -> {
  System.out.println("I have received a chunk of the body of length " + buffer.length());
});
```

传递到handler的对象是一个[Buffer](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html)，该handler可以调用多次，当数据到达时从网络，根据body的大小。

在某些情况下 (例如如果body很小) 想要在内存中缓存整个body如下所示:

```
Buffer totalBuffer = Buffer.buffer();

request.handler(buffer -> {
  System.out.println("I have received a chunk of the body of length " + buffer.length());
  totalBuffer.appendBuffer(buffer);
});

request.endHandler(v -> {
  System.out.println("Full body received, length = " + totalBuffer.length());
});
```

这是这种常见的情况，Vert.x 提供[bodyHandler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#bodyHandler-io.vertx.core.Handler-)来为你做这个。bodyHandler收到所有的body:

```
request.bodyHandler(totalBuffer -> {
  System.out.println("Full body received, length = " + totalBuffer.length());
});
```

##### Pumping requests

请求对象是[ReadStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/ReadStream.html) ，所以你可以pump请求body到任何[WriteStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/WriteStream.html)实例。


##### 处理 HTML 表单

HTML表单可以提交`application/x-www-form-urlencoded`或`multipart/form-data`内容类型。

对于 url 编码形式，像正常的查询参数一样，把表单属性编码在 url 中。

multi-part表单在请求body中编码，因此不可用直到从wire读取了整个body。

Multi-part 表单还可以包含文件上传。

如果您想要获取的属性是multi-part的形式，得到这种表单之前，通过调用[setExpectMultipart](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#setExpectMultipart-boolean-) 设置为true，告诉 Vert.x 你期望的body是read，，然后你应该使用formAttributes获取 ，读取所有body的属性:

```
server.requestHandler(request -> {
  request.setExpectMultipart(true);
  request.endHandler(v -> {
    // The body has now been fully read, so retrieve the form attributes
    MultiMap formAttributes = request.formAttributes();
  });
});
```

##### 处理表单文件上传

Vert.x 还可以处理在multi-part请求body中编码文件上传。

接收文件上传，告诉 Vert.x 期望multi-part表单形式并要求设置[uploadHandler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#uploadHandler-io.vertx.core.Handler-)。

每个上传到服务器上，此handler将调用一次。

传递到handler的对象是一个[HttpServerFileUpload](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerFileUpload.html)实例。

```
server.requestHandler(request -> {
  request.setExpectMultipart(true);
  request.uploadHandler(upload -> {
    System.out.println("Got a file upload " + upload.name());
  });
});
```

上传的文件可能会很大，我们不提供单个缓冲区上传整个数据，因为这可能导致内存消耗殆尽，取而代之的是，上传的数据在块中收到：

```
request.uploadHandler(upload -> {
  upload.handler(chunk -> {
    System.out.println("Received a chunk of the upload of length " + chunk.length());
  });
});
```

上传对象是[ReadStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/ReadStream.html) ，所以你可以pump请求body到任何[WriteStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/WriteStream.html)实例。

如果你只是想要上传文件到磁盘，你可以使用[streamToFileSystem](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerFileUpload.html#streamToFileSystem-java.lang.String-):

```
request.uploadHandler(upload -> {
  upload.streamToFileSystem("myuploads_directory/" + upload.filename());
});
```

> 警告
> 确保您在一个生产系统中检查文件，以避免恶意客户端将文件上传到您的文件系统。查看更多信息的[安全说明](http://vertx.io/docs/vertx-core/java/#_security_notes)。


#### 发送返回响应

服务器的响应对象是[HttpServerResponse](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html)的实例和所得的请求[response](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#response--).

响应对象用于写回 HTTP 客户端的响应。

##### 设置状态代码和消息

response 的默认 HTTP 状态码是200，表示OK.

使用[setStatusCode](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#setStatusCode-int-)来设置不同的代码。

您还可以使用[setStatusMessage](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#setStatusMessage-java.lang.String-)指定一个自定义的状态消息.

如果您不指定一条状态消息，将使用默认的状态代码。

##### 写入 HTTP responses（响应）

使用一个[write](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#write-io.vertx.core.buffer.Buffer-)操作，将数据写入 HTTP responses。

这些可以在响应结束之前多次调用。可以以下面几种方法调用:

用一个缓冲区:

```
HttpServerResponse response = request.response();
response.write(buffer);
```

用一个字符串。在这种情况下将默认使用UTF-8编码。

```
HttpServerResponse response = request.response();
response.write("hello world!");
```

使用一个字符串和编码。在本例的字符串将使用指定的编码。

```
HttpServerResponse response = request.response();
response.write("hello world!", "UTF-16");
```

写入responses是异步的，在写入队列后立即返回。

如果你只是写一个字符串或缓存HTTP response,你可以写它和调用[end](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#end-java.lang.String-)结束response

在响应头第一个调用正在被写入结果的响应
第一次调用将写入结果正在被写入到响应响应头。因此，如果您不使用 HTTP 分块然后之前，必须设置Content-Length标头写的反应，因为它否则就太晚了。如果您使用的 HTTP 分块你不必担心。

第一次调用写结果的响应头被写入响应。因此,如果你不使用HTTP分块，那么header，你必须设置响应的Content-Length。如果您使用HTTP分块你不必担心。

##### 结束 HTTP responses

一旦你完成了 HTTP responses你应该[end](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#end-java.lang.String-)它。

这可以有以下几种方式:

不带任何参数，response 是只是结束。

```
HttpServerResponse response = request.response();
response.write("hello world!");
response.end();
```

它也可以用字符串调用或和缓冲区相同的方式 write。在这种情况下，首先用字符串写入缓冲区，然后和不带参数的一样。例如：


```
HttpServerResponse response = request.response();
response.end("hello world!");
```


##### 关闭底层连接


使用 [close](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#close--) 关闭底层的 TCP 连接.

短连接在 response 结束时， `Vert.x` 将自动关闭 。

长连接的 Vert.x 默认情况下是不会自动的关闭。如果你希望连接保持到空闲时间后关闭，使用 [setIdleTimeout](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerOptions.html#setIdleTimeout-int-) 方法配置。

##### 设置 response headers

可以将 HTTP response headers 通过 response 直接添加到 [headers](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#headers--):

```
HttpServerResponse response = request.response();
MultiMap headers = response.headers();
headers.set("content-type", "text/html");
headers.set("other-header", "wibble");
```

或者你可以使用putHeader

```
HttpServerResponse response = request.response();
response.putHeader("content-type", "text/html").putHeader("other-header", "wibble");
```

Headers 必须添加在所有写入response body之前。

##### 分块 HTTP 响应和传输

Vert.x 支持HTTP 分块传输编码.

表示输出的内容长度不能确定。

把 HTTP response 设置为分块模式，如下所示:

```
HttpServerResponse response = request.response();
response.setChunked(true);
```

默认值为非分块。在分块模式下，每次调用 [write](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#write-io.vertx.core.buffer.Buffer-) 调用时，就会写出一个新的 HTTP 块。

当在分块模式中你也可以写入 HTTP response 传输到response。实际上，这些实际上写入响应的最后块。

若要添加trailers到response，直接添加到trailers.

```
HttpServerResponse response = request.response();
response.setChunked(true);
MultiMap trailers = response.trailers();
trailers.set("X-wibble", "woobble").set("X-quux", "flooble");
```

或使用putTrailer.

```
HttpServerResponse response = request.response();
response.setChunked(true);
response.putTrailer("X-wibble", "woobble").putTrailer("X-quux", "flooble");
```

##### 直接从磁盘或类路径（classpath）提供文件服务

如果你正在编写一个Web服务器，使用AsyncFile打开磁盘上的文件并注入到HTTP response。

或者你可以一气呵成，使用[readFile](http://vertx.io/docs/apidocs/io/vertx/core/file/FileSystem.html#readFile-java.lang.String-io.vertx.core.Handler-)加载并直接写入响应。

另外，Vert.x 提供了一种方法，可以从磁盘或者文件系统操作HTTP response。由底层操作系统支持，这可能在OS中直接从文件到套接字传送字节，而无需通过用户空间（user-space）复制。

对于大文件使用sendFile，通常更有效，但小文件可能较慢。

下面是使用sendFile，提供了一个非常简单的Web服务器:

```
vertx.createHttpServer().requestHandler(request -> {
  String file = "";
  if (request.path().equals("/")) {
    file = "index.html";
  } else if (!request.path().contains("..")) {
    file = request.path();
  }
  request.response().sendFile("web/" + file);
}).listen(8080);
```

发送的文件是异步的,可能无法马上返回。如果你想要该文件写入完成通知，可以使用[sendFile](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html#sendFile-java.lang.String-io.vertx.core.Handler-)

注意
> 如果你在使用的 HTTPS 使用sendFile会通过用户空间 ，因为如果内核将数据直接从磁盘对套接字,没有机会给应用任何加密。

警告

> 如果你要直接使用 Vert.x 写 web 服务器，小心用户访问其他文件路径，使用 Vert.x Web可能更安全，。

当只需要一个文件片段，可以给定从某字节开发，可以通过下面达到：

```
vertx.createHttpServer().requestHandler(request -> {
  long offset = 0;
  try {
    offset = Long.parseLong(request.getParam("start"));
  } catch (NumberFormatException e) {
    // error handling...
  }

  long end = Long.MAX_VALUE;
  try {
    end = Long.parseLong(request.getParam("end"));
  } catch (NumberFormatException e) {
    // error handling...
  }

  request.response().sendFile("web/mybigfile.txt", offset, end);
}).listen(8080);
```

如果想要发送的文件从偏移量开始到结束，您不需要提供长度，在这种情况下你可以这么做:

```
vertx.createHttpServer().requestHandler(request -> {
  long offset = 0;
  try {
    offset = Long.parseLong(request.getParam("start"));
  } catch (NumberFormatException e) {
    // error handling...
  }

  request.response().sendFile("web/mybigfile.txt", offset);
}).listen(8080);
```


##### 注入responses

服务器response是一个[WriteStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/WriteStream.html)实例，可以从任何[ReadStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/ReadStream.html)注入，例如[AsyncFile](http://vertx.io/docs/apidocs/io/vertx/core/file/AsyncFile.html)，[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)，[WebSocket](http://vertx.io/docs/apidocs/io/vertx/core/http/WebSocket.html)或[HttpServerRequest](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html)。

这是一个PUT请求body返回响应的例子，使用pump，即使HTTP请求body比内存大的多，也能正常工作：

```
vertx.createHttpServer().requestHandler(request -> {
  HttpServerResponse response = request.response();
  if (request.method() == HttpMethod.PUT) {
    response.setChunked(true);
    Pump.pump(request, response).start();
    request.endHandler(v -> response.end());
  } else {
    response.setStatusCode(400).end();
  }
}).listen(8080);
```

#### HTTP 压缩

Vert.x 带有 HTTP 压缩开箱即用支持。

这意味着在发送回客户端之前能自动压缩响应的主体。

如果客户端不支持 HTTP 压缩，将没有压缩 body 响应发送回。

这可以同时处理支持 HTTP 压缩的客户端和那些不支持HTTP 压缩的客户端。

若要启用压缩，使用[setCompressionSupported](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerOptions.html#setCompressionSupported-boolean-)配置。

默认情况下不启用压缩。

当启用 HTTP 压缩，服务器将检查客户端是否包括Accept-Encoding的报头，它包含支持的压缩方式。常用的有deflate和 gzip([Web服务器处理HTTP压缩之gzip、deflate压缩](http://quanke.name/2016/05/01/Web服务器处理HTTP压缩之gzip、deflate压缩/))。 Vert.x 两者都支持。

如果服务器将自动压缩与一个支持压缩响应正文中的并将其发送回客户端，就找到这种头。
如果找到这样的报头，服务器将自动压缩发送回客户端。

要知道压缩可能能够减少网络流量，但是是需要消耗更多的 `CPU` 。