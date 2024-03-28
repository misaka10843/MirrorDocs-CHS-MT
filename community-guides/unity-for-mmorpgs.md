---
description: 我们回复 Unity 用户关于在 Unity 中制作 MMORPG 的复制内容
---

# Unity 用于大型多人在线角色扮演游戏(MMORPGs) (Unity for MMORPGs)

许多用户希望使用 Unity / Mirror 制作 MMORPG。虽然 Unity 不是游戏服务器的最佳选择，但它确实带来了很多优势。这篇帖子是我们在 Unity 论坛回复用户有关 [MMOs & Unity](https://forum.unity.com/threads/mmo-server-with-unity.1410480/#post-8868246) 的问题。还要确保查看我们非常古老的 "[如何制作 MMORPG](https://noobtuts.com/articles/how-to-make-a-mmorpg)" 指南，这本质上是 Mirror 最初的起源。\
\
\[...]

你会选择在客户端和服务器端都使用 Unity 的原因是提高生产力。\
共享物理、资源、导航和代码可以节省大量时间。\
有几个较小的 MMO 是这样制作的，您可以将 Unity 服务器实例的 CCU 目标定为约 200-300。例如，Inferna、Samutale 和 Naica 是完全使用 Unity (Mirror) 制作的 MMO。\
[![](https://forum.unity.com/proxy.php?image=https%3A%2F%2Fuser-images.githubusercontent.com%2F16416509%2F178149040-b54e0fa1-3c41-4925-8428-efd0526f8d44.jpg\&hash=06e9b9a127560446c7522abeb8b17e09)](https://forum.unity.com/proxy.php?image=https%3A%2F%2Fuser-images.githubusercontent.com%2F16416509%2F178149040-b54e0fa1-3c41-4925-8428-efd0526f8d44.jpg\&hash=06e9b9a127560446c7522abeb8b17e09)\
\
请注意，您的 tick rate 越低，您可以实现的 CCU 就越多。\
例如，如果您的服务器运行在 60 Hz，那么您有 16 毫秒来更新整个世界。\
如果您的服务器运行在 10 Hz，那么您有 100 毫秒。\
有了 6 倍的更新时间，您可能可以拥有 2-3 倍的 CCU。\
\
这就是许多早期 MMO 在 15 年前已经实现了 500+ CCU 的方式。\
其中许多以 10 Hz 或更低的速率运行。\
缺点是额外的延迟。但是，500 CCU :smile:。\


请记住，如果您关心稳定性、可靠性和规模，Unity 不是游戏服务器的好选择。\
C# 独立项目（在 Unity 之外）可以在 netcore 上运行，这显著更快且更稳定。\
\
在 Unity 之外，您还有更多编程语言选择，这些语言可能更适合高规模/高性能服务器。

- 值得学习 Go（Golang）。这是一种由 Google 开发的并发语言，用于构建可伸缩系统。比使用 C# 更容易进行多线程编程，这得益于 Go 协程（go-routines）。与 C# 一样，Go 也具有垃圾回收机制。

- Rust 也很棒。它具有接近裸机 C/C++ 性能，同时又保证了内存安全性。Rust 的多线程编程也很出色，因为 Rust 会保护你免受数据竞争（与 Go 和 C# 不同）。Rust 不具备垃圾回收机制，因此不会出现需要担心的垃圾回收暂停。

- C# 仍然是一个很好的选择。然而，一些低级网络默认设置并不理想。例如，关于 C# UDP 套接字分配仍在进行讨论[这里](https://github.com/dotnet/runtime/issues/30797)。分配需要进行垃圾回收，这会引入垃圾回收暂停，从而在高规模服务器中引入性能问题。

值得注意的是，Unity 正在逐步转向使用 netcore。这对游戏服务器来说将是一个很好的选择。

**TL;DR:**
Unity 是游戏服务器中速度最慢、最不稳定的解决方案。
但它也是最高效的。
选择其中之一 :thumbsup:
