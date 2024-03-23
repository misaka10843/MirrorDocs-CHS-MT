# TCP 和 UDP

TCP 和 UDP 都是用于通过互联网发送信息的协议 - 实际上，它们是世界上最常用的两种互联网协议：TCP 是在 1970 年代开发的，而 UDP 是在 1980 年代引入的。然而，重要的是要注意 TCP 和 UDP 通信之间的两个关键区别：

- TCP 具有内置的可靠性，确保数据以更高的延迟折衷正确地从 A 发送到 B。
- UDP 具有较低的延迟与其数据传输，但原始协议本质上是不可靠的，并且不保证数据将正确地从 A 发送到 B。

## [TCP (传输控制协议)](https://en.wikipedia.org/wiki/Transmission\_Control\_Protocol) <a href="#tcp-transmission-control-protocol" id="tcp-transmission-control-protocol"></a>

如前所述，TCP 是互联网上最流行的协议之一。TCP 用于 HTTP、SSH、FTP 等许多应用程序。TCP 的核心特性使程序员能够轻松使用该协议，因为程序员可以确保数据将正确地从 A 发送到 B。然而，虽然听起来很棒，但在连接出现故障或遇到严重波动时，可能会引入更高的延迟。

在游戏环境中，TCP 更适用于节奏较慢的游戏，其中延迟不重要，但游戏数据重要。

### 主要特点包括 <a href="#key-features-include" id="key-features-include"></a>

- **可靠性:** 应用程序无需担心丢失数据包。如果数据包丢失，TCP 将重新发送它。所有数据要么传输成功，要么出现错误并关闭连接。
- **顺序性:** TCP 保证每条消息将按发送顺序到达。如果您发送 "a" 然后 "b"，您也将在另一侧按顺序收到 "a" 然后 "b"。
- **面向连接:** TCP 具有连接的概念。连接将保持打开状态，直到客户端或服务器决定关闭它。当连接结束时，客户端和服务器都会收到通知。
- **拥塞控制:** 如果服务器被压倒，TCP 将限制数据以避免拥塞崩溃。

### 使用 TCP 协议的传输（Transports） <a href="#transports" id="transports"></a>

* [Telepathy（心灵感应）](../transports/telepathy-transport.md)
* [WebSockets（网络套接字）](../transports/websockets-transport/)

## [UDP（用户数据报协议）](https://en.wikipedia.org/wiki/User\_Datagram\_Protocol) <a href="#udp-user-datagram-protocol" id="udp-user-datagram-protocol"></a>

UDP 用于实时应用程序，如快节奏动作游戏或语音通信（VoIP），其中低延迟比可靠性更重要。应用示例包括 Skype、Discord、Zoom 等许多其他应用。&#x20;

在游戏环境中，UDP 的原始力量可以被利用，允许更好地控制数据的发送方式，使非关键数据可以更快地发送。这反过来使 UDP 更适合快节奏游戏，其中服务器和客户端之间的延迟很重要，如果丢失了一些数据包，游戏可以恢复。

### 主要特点包括 <a href="#key-features-include-1" id="key-features-include-1"></a>

* **低延迟（Low Latency）:** UDP 更快，因为它不需要等待远程端确认数据包。相反，它可以继续发送新的数据包。它也被称为“散射”协议，因为它会向客户端发送数据，而不保证它们实际上会收到数据。
* **通道支持（Channel support）:** 通道允许不同的传输类型。根据实现，一个通道可以用于需要到达目的地的关键数据，而另一个通道可以用于“发送并忘记”数据传输，没有可靠性。
* **不同的数据包类型（Different packet types）:** 根据 UDP 协议之上的实现，一些传输提供不同的数据包发送方法，如可靠有序（Reliable Ordered）、可靠无序（Reliable Unordered）、不可靠（Unreliable）等，具体取决于实现。可靠的 UDP 传输取决于实现，但通常是模仿 TCP 的可靠性系统。

### 使用 UDP 协议的传输（Transports） <a href="#transports-1" id="transports-1"></a>

* [Ignorance（无知）](../transports/ignorance.md)
* [KCP](../transports/kcp-transport.md)
* [LiteNetLib（轻量级网络库）](../transports/litenetlib-transport.md)

## 选择权在你手中 <a href="#the-choice-is-yours" id="the-choice-is-yours"></a>

Mirror 是独立于传输的，它们可以简单地添加到你的 NetworkManager GameObject 中。Mirror 默认带有 [KCP](../transports/kcp-transport.md) 传输（除了 WebGL 平台）和 [Websocket](../transports/websockets-transport/) 传输（WebGL）。查看 [传输](../transports/) 页面了解更多关于传输的信息。

选择最适合你和你的游戏的传输方式。我们建议在最终决定之前对游戏的网络进行性能分析并收集真实数据。{/*examples*/}

