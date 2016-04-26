# 编写 TCP 服务器

#### 创建 TCP 服务器

使用最简单的方法来创建一个 TCP 服务器，使用所有默认选项如下所示:

```
NetServer server = vertx.createNetServer();
```


#### 配置 TCP 服务器

如果你不想默认值，可以将服务器配置通过传入一个[NetServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html)实例来创建它:

```
NetServerOptions options = new NetServerOptions().setPort(4321);
NetServer server = vertx.createNetServer(options);
```
#### 启动服务器监听

使用[listen](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#listen--)告诉服务监听传入的请求

告诉要听的主机和端口作为选项中指定的服务器:

需要在选项（`NetServerOptions`）中指定的主机和端口:

```
NetServer server = vertx.createNetServer();
server.listen();
```

或在调用listen中指定的主机和端口，忽略`NetServerOptions`配置:

```
NetServer server = vertx.createNetServer();
server.listen(1234, "localhost");
```

默认主机是`0.0.0.0`，意味着 '监听所有可用的地址' ，默认端口是0，这是一个特殊值，告诉服务器随机找一个未使用的本地端口使用。

真实的绑定是异步的，所以服务器可能不会实际被侦听，直到有一段时间后，调用返回。

如果想要listen实际监听后通知你，可以提供listen调用处理程序。例如:

```
NetServer server = vertx.createNetServer();
server.listen(1234, "localhost", res -> {
  if (res.succeeded()) {
    System.out.println("Server is now listening!");
  } else {
    System.out.println("Failed to bind!");
  }
});
```

#### 监听随机端口

如果`0`用作的监听端口，则服务器将找到一个未使用的随机端口监听。

若要找出服务器正在监听的真正端口，您可以调用[actualPort](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#actualPort--).

```
NetServer server = vertx.createNetServer();
server.listen(0, "localhost", res -> {
  if (res.succeeded()) {
    System.out.println("Server is now listening on actual port: " + server.actualPort());
  } else {
    System.out.println("Failed to bind!");
  }
});
```


#### 传入连接通知

若要连接时通知您需要设置[connectHandler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#connectHandler-io.vertx.core.Handler-):

```
NetServer server = vertx.createNetServer();
server.connectHandler(socket -> {
  // Handle the connection in here
});
```

当进行连接时的处理程序将调用[Netsocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)实例。


这是一个类似于socket-like的接口的真实连接，并且允许你读写数据以及做其他各种类似的事情，比如关闭套接字。

#### 从Socket读取数据

从socket读取数据要在socket上设置[handler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#handler-io.vertx.core.Handler-)。

每次在socket上接收到数据Buffer实例，将调用此处理程序。

```
NetServer server = vertx.createNetServer();
server.connectHandler(socket -> {
  socket.handler(buffer -> {
    System.out.println("I received some bytes: " + buffer.length());
  });
});
```

#### 数据写入socket

使用[write](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#write-io.vertx.core.buffer.Buffer-)写到socket.

```
Buffer buffer = Buffer.buffer().appendFloat(12.34f).appendInt(123);
socket.write(buffer);

// Write a string in UTF-8 encoding
socket.write("some data");

// Write a string using the specified encoding
socket.write("some data", "UTF-16");
```

写操作是异步的，调用返回之后可能不会发生。

####关闭的处理程序（handler）

如果你想要关闭socket时得到通知，可以设置[closeHandler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#closeHandler-io.vertx.core.Handler-) :

```
socket.closeHandler(v -> {
  System.out.println("The socket has been closed");
});
```

#### 处理异常

您可以设置[exceptionHandler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#exceptionHandler-io.vertx.core.Handler-)接收socket发生的任何异常。

#### Event bus写handler

每个socket自动注册event bus上的handler，当这个handler接收到任何buffers时，它会将它们写入到本身。

这使您可以将数据写到socket，它可能是在完全不同的verticle或甚至在不同的 Vert.x 实例，通过将buffers发送到该处理程序的地址。

由[writeHandlerID](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#writeHandlerID--)给出了处理程序的地址

#### 本地和远程地址

可以使用[localAddress](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#localAddress--)检索[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)的本地地址.

可以使用[remoteAddress](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#remoteAddress--)检索远程地址 (即地址连接的另一端) 的[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html).

#### 从classpath发送文件或资源

直接使用[sendFile](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#sendFile-java.lang.String-)就可以将文件写入socket。这是非常有效的发送文件的方法，因为它可以由操作系统内核直接支持。

```
socket.sendFile("myfile.dat");
```

#### Streaming sockets

NetSocket的实例也是ReadStream和WriteStream的实例，因此他们可以被用于泵送数据，可以从其他的读和写streams。

详细信息，请参阅Streams章。

#### 升级到 SSL/TLS 连接

非 `SSL/TLS` 连接可以使用[upgradeToSsl](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#upgradeToSsl-io.vertx.core.Handler-)升级到 `SSL/TLS`。

服务器或客户端必须配置为 `SSL/TLS` 才能正常工作。详细信息请参阅关于 `SSL/TLS` 章节。

#### 关闭一个 TCP 服务器

叫[close](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#close--)来关闭服务器。关闭服务器关闭任何打开的连接，并释放所有的服务器资源。

关闭是异步的，可能无法立即返回。如果你想要关闭已完成然后通知，可以通过handler做到。

当关闭已全面完成，然后将调用此处理程序。

```
server.close(res -> {
  if (res.succeeded()) {
    System.out.println("Server is now closed");
  } else {
    System.out.println("close failed");
  }
});
```

#### Verticles 自动清理

如果您正在从 `verticles`内创建 `TCP` 服务器和客户端，`verticle`取消部署时这些服务器和客户端将被自动关闭。

#### Scaling - sharing TCP 服务器

任何TCP服务器的处理器总是在相同的事件循环线程执行。

这意味着，如果在多核的服务器上运行，而你只部署一个实例，那么最多使用一个核心。

为了利用你的服务器更多的核心，你将需要部署服务器的多个实例。

您可以以编程方式在代码中实例化多个实例：

```
for (int i = 0; i < 10; i++) {
  NetServer server = vertx.createNetServer();
  server.connectHandler(socket -> {
    socket.handler(buffer -> {
      // Just echo back the data
      socket.write(buffer);
    });
  });
  server.listen(1234, "localhost");
}
```

或者，如果您正在使用verticles你可以简单地通过使用命令行选项`-instances`部署服务器verticle的多个实例：

```
vertx run com.mycompany.MyVerticle -instances 10
```

或以编程方式部署verticle

```
DeploymentOptions options = new DeploymentOptions().setInstances(10);
vertx.deployVerticle("com.mycompany.MyVerticle", options);
```

一旦你这样做，你会发现echo服务器的功能与以前的功能相同，但可以利用您在您的服务器上的所有核心，可以处理更多的工作。

在这一点上你可能会问自己' 你怎么能有多个服务器监听同一主机和端口?尝试部署多个实例，肯定会端口冲突?'

Vert.x 确实有点神奇，如:

当你在同一个主机上部署另一个服务器，作为一个现有的服务器，它实际上并没有尝试在同一个主机/端口上创建一个新的服务器。

相反它内部维护只是一台服务器，将连接通过循环的方式分配处理程序。

因此 Vert.x TCP 服务部署可以超过可用CPU内核，每个实例是单个线程。
