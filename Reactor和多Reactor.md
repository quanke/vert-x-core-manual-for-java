# Reactor和多Reactor

之前提到Vert.x API是事件驱动 - 当他们都可用时，Vert.x传递事件给处理程序。

在大多数情况下Vertx要求使用一种称为event loop线程的处理程序。

如无有 Vert.x 或您的应用程序块中，event loop可以欢快地运行将事件传递给不同的处理程序提供事件陆续到达。

因为没有阻塞，event loop可以在短时间内提供大量的事件。例如一个单一的event loop可以非常迅速地处理成千上万的 HTTP 请求。

我们把这个叫做反应器模式（[Reactor Pattern](http://en.wikipedia.org/wiki/Reactor_pattern)）.

你可能会有之前听说过-例如 Node.js 实现此模式。

标准的Reactor所有事件都运行在单一事件循环线程。

单个线程的麻烦是在任何一个时间它只能运行在单一的核心上(例如 Node.js 应用，如果想要实现多线程你要做很多事 。

而Vert.x 不同。不是单事件循环，每个 Vertx 实例都维护若干个事件循环。默认情况下，我们选择数量基于在机器上可用的内核数，但可以自己设置。

与 Node.js 不同是Vertx进程是可配置的，与 Node.js 不同

我们称这种模式多反应器（Multi-Reactor）模式，以区别于单线程的反应器模式。

*注意：即使 Vertx 实例维护多个事件循环，任何特定的处理程序将永远不会被同时执行，在大多数情况下 (除了 [worker verticles](http://vertx.io/docs/vertx-core/java/#worker_verticles)) 将始终使用完全相同的事件循环调用。*