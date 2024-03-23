# TCP 和 UDP

TCP 和 UDP 都是用于通过互联网发送信息的协议-事实上，它们是世界上最常用的两种互联网协议：TCP 是在 20 世纪 70 年代开发的，而 UDP 是在 20 世纪 80 年代引入的。 但是，重要的是要注意 TCP 和 UDP 通信之间的两个关键区别：

- TCP 具有内置的可靠性，确保数据正确地从 A 到达 B，同时具有更高的延迟。
- UDP 的数据传输延迟较低，但原始协议本质上是不可靠的，不能保证数据将正确地从 A 发送到 B。

## [传输控制协议](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)<a href="#tcp-transmission-control-protocol" id="tcp-transmission-control-protocol"></a>

如前所述，TCP 是互联网上最流行的协议。 TCP 用于 HTTP，SSH，FTP 和更多的应用程序。 TCP 的核心特性使程序员可以轻松地使用该协议，因为程序员可以确保数据从 A 正确地发送到 B。 然而，虽然这听起来很棒，但当连接出现故障或遇到严重动荡时，它可能会引入更高的延迟。

在游戏环境中，TCP 更适合节奏较慢的游戏，其中延迟并不重要，但游戏数据很重要。

### 主要功能包括<a href="#key-features-include" id="key-features-include"></a>

- **可靠：**应用程序不必担心丢失数据包。 如果数据包丢失，TCP 将重新发送它。所有数据要么成功传输，要么出现错误并关闭连接。
- **Sequenced：**TCP 保证每一条消息都将按照发送的顺序到达。 如果你发送"a"，然后"b"，你会收到"a"，然后"b"在另一边。
- **面向连接：**TCP 有连接的概念。 连接将保持打开状态，直到客户端或服务器决定关闭它。当连接结束时，客户端和服务器都会收到通知。
- **拥塞控制：**如果服务器不堪重负，TCP 将限制数据以避免拥塞崩溃。

### 使用 TCP 协议传输<a href="#transports" id="transports"></a>

- [心灵感应](../transports/telepathy-transport.md)
- [WebSockets](../transports/websockets-transport/)

## [UDP（用户数据报协议）](https://en.wikipedia.org/wiki/User_Datagram_Protocol)<a href="#udp-user-datagram-protocol" id="udp-user-datagram-protocol"></a>

UDP 用于实时应用，如快节奏的动作游戏或 IP 语音（VoIP），其中低延迟比可靠性更重要。 应用程序的例子包括 Skype，Discord，Zoom 和许多其他。x20;

在游戏环境中，可以利用 UDP 的原始功能来更好地控制数据的发送方式，从而更快地发送非关键数据。 这反过来又使 UDP 更适合快节奏的游戏，其中服务器和客户端之间的延迟很重要，如果丢失了一些数据包，游戏可以恢复。

### 主要功能包括<a href="#key-features-include-1" id="key-features-include-1"></a>

- **低延迟：**UDP 更快，因为它不需要等待远程端确认数据包。 相反，它可以一个接一个地发送新的数据包。 它也被称为"散射"协议，因为它只会向客户端发送数据，而不能保证他们实际上会获得数据。
- **通道支持：**通道允许不同的交付类型。 根据具体实现，一个通道可以用于需要到达目的地的关键数据，而另一个通道可以用于"发送并忘记"数据传输，没有任何可靠性。
- **不同的数据包类型：**根据 UDP 协议的实现，某些传输提供不同的数据包发送方法，例如可靠有序，可靠无序，不可靠等。 可靠的 UDP 传输取决于实现，但它通常是在 TCP 的可靠性系统之后建模的。

### 使用 UDP 协议传输<a href="#transports-1" id="transports-1"></a>

- [无知](../transports/ignorance.md)
- [KCP](../transports/kcp-transport.md)
- [LiteNetLib](../transports/litenetlib-transport.md)

## 选择权在你<a href="#the-choice-is-yours" id="the-choice-is-yours"></a>

Mirror 是独立于传输的，它们可以简单地添加到您的 NetworkManager GameObject 中。 默认情况下，Mirror 带有[KCP](../transports/kcp-transport.md)传输*（WebGL 以外的所有平台）*和[Websocket](../transports/websockets-transport/)*（WebGL）*传输。 有关运输的更多信息，请参阅[运输](../transports/)页面。

选择最适合您和您的游戏的任何运输工具。 我们建议您在做出最终决定之前分析游戏的网络并收集真实世界的数字。
