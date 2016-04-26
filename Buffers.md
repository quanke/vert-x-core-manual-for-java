# 缓冲区


大多数数据是使用缓冲区抛入Vert.x内。

缓冲区是零个或多个字节，可以读取或写入，并自动扩展，以适应任何字节写入它。你或许可以认为缓冲区是智能字节数组。

#### 创建缓冲区

缓冲区可以通过静态的[Buffer.buffer](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#buffer--)方法创建。

可以从字符串或字节数组，初始化缓冲区，也可以创建空缓冲区。

下面是创建缓冲区的一些示例:

创建一个新的空缓冲区:

```
Buffer buff = Buffer.buffer();
```

从一个字符串创建一个缓冲区。字符串将在缓冲区中使用 utf-8 编码。

```
Buffer buff = Buffer.buffer("some string");
```

从字符串创建缓冲区：该字符串将使用指定的编码，例如：

```
Buffer buff = Buffer.buffer("some string", "UTF-16");
```

从 byte[]创建一个缓冲区。

```
byte[] bytes = new byte[] {1, 3, 5};
Buffer buff = Buffer.buffer(bytes);
```

创建一个初始大小的缓冲区。如果你知道你的缓冲区将有一定数量的数据写入，你可以在创建缓冲区时指定缓冲区大小。这使得缓冲最初分配多的内存，比缓冲区自动调整多次作为数据写入它更有效。

请注意，这种方法创建的缓冲区是空的。它不能创建大小为零的缓冲。

```
Buffer buff = Buffer.buffer(10000);
```

#### 写入缓冲区

有两种方法来写入缓冲区：添加、和随机存取。在任何情况下，缓冲区将自动扩展，以包括字节。用缓冲区获取IndexOutOfBoundsException这是不可能的。

##### 添加到缓冲区

要添加到缓冲区，您可以使用appendXXX方法。添加各种不同类型存在的方法。

appendXXX方法的返回值是缓冲区本身，以便这些可以将链接:

```
Buffer buff = Buffer.buffer();

buff.appendInt(123).appendString("hello\n");

socket.write(buff);
```

##### 写入随机存取缓冲区

使用setXXX方法，在特定的索引中，可以写入缓冲区中。Set 的方法存在各种不同的数据类型。所有的设置的方法取索引作为第一个参数-这代表缓冲区中的位置从哪里开始写入数据。

缓冲区将总是需要时扩展，以容纳数据。

```
Buffer buff = Buffer.buffer();

buff.setInt(1000, 123);
buff.setString(0, "hello");
```

#### 从缓冲区读取

使用getXXX方法从缓冲区读取数据。获得方法存在不同的数据类型。这些方法的第一个参数是获取数据的缓冲区中的一个索引。

```
Buffer buff = Buffer.buffer();
for (int i = 0; i < buff.length(); i += 4) {
  System.out.println("int value at " + i + " is " + buff.getInt(i));
}
```

#### 使用无符号数字

无符号的数字可以通过getUnsignedXXX、 appendUnsignedXXX和setUnsignedXXX的方法读取或追加/套到缓冲区。这是有用的，当实现一个编解码器的网络协议优化，以尽量减少带宽消耗。

在以下示例中， 200 是设置在指定的位置只有一个字节:

```
Buffer buff = Buffer.buffer(128);
int pos = 15;
buff.setUnsignedByte(pos, (short) 200);
System.out.println(buff.getUnsignedByte(pos));
```

控制台显示 '200'。

#### 缓冲区长度

使用[length](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#length--)来获取缓冲区的长度。缓冲区的长度是在缓冲区的最大索引 + 1 字节的索引。

#### 复制缓冲区

使用[copy](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#copy--)，复制缓冲区

#### 切片的缓冲区

切片的缓冲区是一个新的缓冲区，背对着原来的缓冲区，即它将不复制的基础数据。使用[slice](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#slice--)创建切片的缓冲区

#### 缓冲区重新使用

一个缓冲区写入套接字或其它类似的地方之后，他们不能被重新使用。
