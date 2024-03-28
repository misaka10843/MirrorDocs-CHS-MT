---
描述: https://github.com/vis2k/Telepathy
---

# Telepathy 传输 (Transport)

简单、基于消息的、C# 编写的 MMO 规模 TCP 网络。没有魔法。

* Telepathy 被设计遵循 [KISS 原则](https://en.wikipedia.org/wiki/KISS\_principle)。
* Telepathy 快速且极其可靠，专为 [MMO](https://assetstore.unity.com/packages/templates/systems/ummorpg-51212) 规模的网络设计。
* Telepathy 使用帧，因此发送的任何内容都将以相同的方式接收。
* Telepathy 是原生的 C#，也可在 Unity3D 中使用。
* Telepathy 可在 [GitHub](https://github.com/vis2k/Telepathy) 上获取。

## 什么使 Telepathy 特殊？ <a href="#what-makes-telepathy-special" id="what-makes-telepathy-special"></a>

Telepathy 最初是为 [uMMORPG](https://assetstore.unity.com/packages/templates/systems/ummorpg-51212) 设计的，在经历了 3 年的 UDP 地狱之后。

我们需要一个具有以下特点的库：

* 稳定且无 bug：Telepathy 仅使用 400 行代码。没有魔法。
* 高性能：Telepathy 可处理成千上万的连接和数据包。
* 并发性：Telepathy 对每个连接使用一个线程。它可以充分利用多核处理器。
* 简单：Telepathy 处理一切。你只需要调用 Connect/GetNextMessage/Disconnect。
* 基于消息：如果我们发送 10 字节，然后 2 字节，那么另一端将分别接收 10 字节和 2 字节，而不是一次性接收 12 字节。

制作 MMORPG 游戏非常困难，我们创建了 Telepathy，这样我们就再也不必担心底层网络了。

## 关于... <a href="#what-about" id="what-about"></a>

* 异步套接字：在我们的基准测试中表现并不比较好。
* ConcurrentQueue：.NET 3.5 的兼容性对 Unity 很重要。无论如何，它也不比我们的 SafeQueue 更快。
* UDP vs. TCP：《Minecraft》和《魔兽世界》是有史以来最大的两款多人在线游戏，它们都使用 TCP 网络。这是有原因的。
