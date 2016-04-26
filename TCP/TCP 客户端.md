# 编写 TCP 客户端

#### 创建 TCP 客户端

最简单的方法来创建一个 TCP 客户端，使用默认选项如下所示:

```
NetClient client = vertx.createNetClient();
```

#### 配置 TCP 客户端

如果你不想使用默认值，则创建TCP 客户端时，通过传入[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)实例可以配置:

```
NetClientOptions options = new NetClientOptions().setConnectTimeout(10000);
NetClient client = vertx.createNetClient(options);
```

#### 建立连接

要连接到的服务器可以使用[connect](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClient.html#connect-int-java.lang.String-io.vertx.core.Handler-)，指定端口和服务器地址和一个handler，handler包含 [NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html) 和成功或者失败的结果。

```
NetClientOptions options = new NetClientOptions().setConnectTimeout(10000);
NetClient client = vertx.createNetClient(options);
client.connect(4321, "localhost", res -> {
  if (res.succeeded()) {
    System.out.println("Connected!");
    NetSocket socket = res.result();
  } else {
    System.out.println("Failed to connect: " + res.cause().getMessage());
  }
});
```

#### 配置自动重连

无法连接可以配置为自动重连。配置[setReconnectInterval](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html#setReconnectInterval-long-)和[setReconnectAttempts](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html#setReconnectAttempts-int-).

> 注意
目前 Vert.x 不会尝试重新连接，如果连接失败，重连和重连间隔仅适用于创建初始连接。

```
NetClientOptions options = new NetClientOptions().
    setReconnectAttempts(10).//重连次数
    setReconnectInterval(500)//重连间隔

NetClient client = vertx.createNetClient(options);
```

默认情况下，自动重连不开启。

#### 配置服务器和客户端使用 SSL/TLS 

TCP 客户端和服务器可以配置使用TLS（[安全传输层协议](http://baike.baidu.com/link?url=PPiSbb4Qa9EBJQ8PABddBZGoD1jx28958c9gksFY92a0BHpWJEgLau-pF5xwMrhyB-5TlJeqJCk-ZZe0wllKTq)）-TLS 的早期版本被称为 SSL。

无论使用 SSL/TLS服务器和客户端 Api 是相同的，是否启用，通过[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)或[NetServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html)实例配置。

##### 在服务器上启用 SSL/TLS

通过[ssl](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html#setSsl-boolean-)启用 SSL/TLS.

默认是禁用的。

##### 为服务器指定密钥/证书

SSL / TLS服务器通常提供证书给客户以验证他们的身份。

针对服务器的几种配置证书/密钥的方式:

第一种方法是通过指定 `Java` 密钥存储并包含证书和私钥的位置。

Java 密钥存储库可以使用JDK附带的实用程序[keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/solaris/keytool.html)工具管理。
此外应提供密钥存储库的密码:

```
NetServerOptions options = new NetServerOptions().setSsl(true).setKeyStoreOptions(
    new JksOptions().
        setPath("/path/to/your/server-keystore.jks").
        setPassword("password-of-your-keystore")
);
NetServer server = vertx.createNetServer(options);
```

或者，你可以把你自己的钥匙储存为一个缓冲区，直接提供：


```
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-keystore.jks");
JksOptions jksOptions = new JksOptions().
    setValue(myKeyStoreAsABuffer).
    setPassword("password-of-your-keystore");
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setKeyStoreOptions(jksOptions);
NetServer server = vertx.createNetServer(options);
```

还可以以类似 `JKS` 方式 `PKCS #12` 格式 (http://en.wikipedia.org/wiki/PKCS_12)密钥存储加载密钥/证书，通常具有.pfx或.p12扩展名:

```
NetServerOptions options = new NetServerOptions().setSsl(true).setPfxKeyCertOptions(
    new PfxOptions().
        setPath("/path/to/your/server-keystore.pfx").
        setPassword("password-of-your-keystore")
);
NetServer server = vertx.createNetServer(options);
```

此外支持缓冲区配置:

```
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-keystore.pfx");
PfxOptions pfxOptions = new PfxOptions().
    setValue(myKeyStoreAsABuffer).
    setPassword("password-of-your-keystore");
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setPfxKeyCertOptions(pfxOptions);
NetServer server = vertx.createNetServer(options);
```

另一种方式，分别使用`.pem`文件提供服务器私钥和证书。

```
NetServerOptions options = new NetServerOptions().setSsl(true).setPemKeyCertOptions(
    new PemKeyCertOptions().
        setKeyPath("/path/to/your/server-key.pem").
        setCertPath("/path/to/your/server-cert.pem")
);
NetServer server = vertx.createNetServer(options);
```

此外支持缓冲区配置:

```
Buffer myKeyAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-key.pem");
Buffer myCertAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-cert.pem");
PemKeyCertOptions pemOptions = new PemKeyCertOptions().
    setKeyValue(myKeyAsABuffer).
    setCertValue(myCertAsABuffer);
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setPemKeyCertOptions(pemOptions);
NetServer server = vertx.createNetServer(options);
```

请牢记，pem 的配置， 私钥是不加密的。

##### 指定信任服务器

SSL / TLS服务器使用证书授权可以以验证客户端的身份。

几种针对服务器的证书授权的配置方法:


此外应提供密钥存储库的密码:

Java trust存储库可以使用 JDK附带的实用程序[keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/solaris/keytool.html)工具管理。

此外应提供trust存储库的密码:

```
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setClientAuth(ClientAuth.REQUIRED).
    setTrustStoreOptions(
        new JksOptions().
            setPath("/path/to/your/truststore.jks").
            setPassword("password-of-your-truststore")
    );
NetServer server = vertx.createNetServer(options);
```

或者，你可以把你自己的trust储存为一个缓冲区，直接提供：

```
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.jks");
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setClientAuth(ClientAuth.REQUIRED).
    setTrustStoreOptions(
        new JksOptions().
            setValue(myTrustStoreAsABuffer).
            setPassword("password-of-your-truststore")
    );
NetServer server = vertx.createNetServer(options);
```

还可以以类似 `JKS` 方式 `PKCS #12` 格式 (http://en.wikipedia.org/wiki/PKCS_12) `trust` 存储加载密钥/证书，通常具有.pfx或.p12扩展名:

```
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setClientAuth(ClientAuth.REQUIRED).
    setPfxTrustOptions(
        new PfxOptions().
            setPath("/path/to/your/truststore.pfx").
            setPassword("password-of-your-truststore")
    );
NetServer server = vertx.createNetServer(options);
```

此外支持缓冲区配置:

```
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.pfx");
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setClientAuth(ClientAuth.REQUIRED).
    setPfxTrustOptions(
        new PfxOptions().
            setValue(myTrustStoreAsABuffer).
            setPassword("password-of-your-truststore")
    );
NetServer server = vertx.createNetServer(options);
```


另一种方式，使用列表的.pem文件提供服务器证书授权

```
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setClientAuth(ClientAuth.REQUIRED).
    setPemTrustOptions(
        new PemTrustOptions().
            addCertPath("/path/to/your/server-ca.pem")
    );
NetServer server = vertx.createNetServer(options);
```

此外支持缓冲区配置:

```
Buffer myCaAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-ca.pfx");
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setClientAuth(ClientAuth.REQUIRED).
    setPemTrustOptions(
        new PemTrustOptions().
            addCertValue(myCaAsABuffer)
    );
NetServer server = vertx.createNetServer(options);
```

##### 在客户端上启用 SSL/TLS

Net 客户端也可以轻松地配置为使用 SSL。他们有相同的 API，当使用 SSL时 使用的是标准的sockets。

NetClient调用setSSL(true) 函数启用 SSL。

##### 客户端信任配置

在客户端，如果[trustALl](http://vertx.io/docs/apidocs/io/vertx/core/net/ClientOptionsBase.html#setTrustAll-boolean-)设置为 true，客户端将信任所有的服务器证书。连接仍将被加密，但这种模式是易受攻击的。请谨慎使用。默认值为 false。

```
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setTrustAll(true);
NetClient client = vertx.createNetClient(options);
```

如果未设置[trustAll](http://vertx.io/docs/apidocs/io/vertx/core/net/ClientOptionsBase.html#setTrustAll-boolean-)，客户端必须配置trust store 和应该包含该客户端信任的服务器证书。

同服务器配置，配置客户端信任可以分几个方面:

第一种方法是通过指定包含证书授权 Java  trust-store 的位置。

这只是标准 Java 密钥库，密钥存储与服务器端相同。由 [jks options](http://vertx.io/docs/apidocs/io/vertx/core/net/JksOptions.html) 使用函数[path](http://vertx.io/docs/apidocs/io/vertx/core/net/JksOptions.html#setPath-java.lang.String-)设置的客户端trust-store位置。如果服务器不是客户端信任存储区中的连接过程中提供的证书，则连接尝试不会成功。

```
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setTrustStoreOptions(
        new JksOptions().
            setPath("/path/to/your/truststore.jks").
            setPassword("password-of-your-truststore")
    );
NetClient client = vertx.createNetClient(options);
```

此外支持缓冲区配置:

```
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.jks");
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setTrustStoreOptions(
        new JksOptions().
            setValue(myTrustStoreAsABuffer).
            setPassword("password-of-your-truststore")
    );
NetClient client = vertx.createNetClient(options);
```

还可以以类似 `JKS` 方式 `PKCS #12` 格式 (http://en.wikipedia.org/wiki/PKCS_12) `trust` 存储加载密钥/证书，通常具有.pfx或.p12扩展名:

```
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setPfxTrustOptions(
        new PfxOptions().
            setPath("/path/to/your/truststore.pfx").
            setPassword("password-of-your-truststore")
    );
NetClient client = vertx.createNetClient(options);
```
此外支持缓冲区配置:

```
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.pfx");
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setPfxTrustOptions(
        new PfxOptions().
            setValue(myTrustStoreAsABuffer).
            setPassword("password-of-your-truststore")
    );
NetClient client = vertx.createNetClient(options);
```

另一种方式，使用列表的.pem文件提供服务器证书授权

```
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setPemTrustOptions(
        new PemTrustOptions().
            addCertPath("/path/to/your/ca-cert.pem")
    );
NetClient client = vertx.createNetClient(options);
```

此外支持缓冲区配置:

```
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/ca-cert.pem");
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setPemTrustOptions(
        new PemTrustOptions().
            addCertValue(myTrustStoreAsABuffer)
    );
NetClient client = vertx.createNetClient(options);
```

##### 为客户端指定密钥/证书

如果服务器要求客户端身份验证，然后连接时，客户端必须向服务器提交它自己的证书。客户端可以配置几个方面:

第一种方法是通过指定 Java 密钥库包含密钥和证书的位置。这是只是常规 Java 的密钥存储库。客户端密钥库位置是通过使用函数[path](http://vertx.io/docs/apidocs/io/vertx/core/net/JksOptions.html#setPath-java.lang.String-)上[jks options](http://vertx.io/docs/apidocs/io/vertx/core/net/JksOptions.html)设置.

```
NetClientOptions options = new NetClientOptions().setSsl(true).setKeyStoreOptions(
    new JksOptions().
        setPath("/path/to/your/client-keystore.jks").
        setPassword("password-of-your-keystore")
);
NetClient client = vertx.createNetClient(options);
```

此外支持缓冲区配置:

```
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-keystore.jks");
JksOptions jksOptions = new JksOptions().
    setValue(myKeyStoreAsABuffer).
    setPassword("password-of-your-keystore");
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setKeyStoreOptions(jksOptions);
NetClient client = vertx.createNetClient(options);
```

还可以以类似 `JKS` 方式 `PKCS #12` 格式 (http://en.wikipedia.org/wiki/PKCS_12) `trust` 存储加载密钥/证书，通常具有.pfx或.p12扩展名:

```
NetClientOptions options = new NetClientOptions().setSsl(true).setPfxKeyCertOptions(
    new PfxOptions().
        setPath("/path/to/your/client-keystore.pfx").
        setPassword("password-of-your-keystore")
);
NetClient client = vertx.createNetClient(options);
```

此外支持缓冲区配置:

```
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-keystore.pfx");
PfxOptions pfxOptions = new PfxOptions().
    setValue(myKeyStoreAsABuffer).
    setPassword("password-of-your-keystore");
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setPfxKeyCertOptions(pfxOptions);
NetClient client = vertx.createNetClient(options);
```

另一种方式，分别使用`.pem`文件提供服务器私钥和证书。

```
NetClientOptions options = new NetClientOptions().setSsl(true).setPemKeyCertOptions(
    new PemKeyCertOptions().
        setKeyPath("/path/to/your/client-key.pem").
        setCertPath("/path/to/your/client-cert.pem")
);
NetClient client = vertx.createNetClient(options);
```

此外支持缓冲区配置:

```
Buffer myKeyAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-key.pem");
Buffer myCertAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-cert.pem");
PemKeyCertOptions pemOptions = new PemKeyCertOptions().
    setKeyValue(myKeyAsABuffer).
    setCertValue(myCertAsABuffer);
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setPemKeyCertOptions(pemOptions);
NetClient client = vertx.createNetClient(options);
```

请牢记，pem 的配置， 私钥是不加密的。

##### 吊销认证授权

被吊销的证书不再应信任，信任可以配置为使用证书吊销列表 (CRL)。[crlPath](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html#addCrlPath-java.lang.String-)配置要使用的 crl 列表:

```
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setTrustStoreOptions(trustOptions).
    addCrlPath("/path/to/your/crl.pem");
NetClient client = vertx.createNetClient(options);
```

此外支持缓冲区配置:

```
Buffer myCrlAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/crl.pem");
NetClientOptions options = new NetClientOptions().
    setSsl(true).
    setTrustStoreOptions(trustOptions).
    addCrlValue(myCrlAsABuffer);
NetClient client = vertx.createNetClient(options);
```

##### 配置加密套件

默认情况下，TLS配置将使用JVM运行Vert.x.的加密套件这种密码套件可以用一组启用密码进行配置：

```
NetServerOptions options = new NetServerOptions().
    setSsl(true).
    setKeyStoreOptions(keyStoreOptions).
    addEnabledCipherSuite("ECDHE-RSA-AES128-GCM-SHA256").
    addEnabledCipherSuite("ECDHE-ECDSA-AES128-GCM-SHA256").
    addEnabledCipherSuite("ECDHE-RSA-AES256-GCM-SHA384").
    addEnabledCipherSuite("CDHE-ECDSA-AES256-GCM-SHA384");
NetServer server = vertx.createNetServer(options);
```

加密套件可以在NetServerOptions或NetClientOptions配置中指定。

