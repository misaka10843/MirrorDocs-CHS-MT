---
description: https://github.com/SoftwareGuy/Ignorance
---

# Ignorance (Ignorance)

## 什么是 Ignorance? <a href="#what-is-ignorance" id="what-is-ignorance"></a>

Ignorance 是一个可靠的 UDP 传输层，利用原生的 ENET C 网络库，通过一个[ENet-CSharp 的自定义分支](https://github.com/SoftwareGuy/ENet-CSharp)提供了一个可靠和不可靠的序列化 UDP 传输，适用于 64 位桌面操作系统（Windows、Mac OS 和 Linux）和移动操作系统（Apple iOS 和 Android）。它还支持最多 255 个通道和同时连接 4096 个客户端。

ENET 是一个稳定可靠的 UDP C++ 网络库，已经非常成熟和稳定。Unity 的 LLAPI 需要一个替代方案。Ignorance 的设计目标是填补这个空白，为 Mirror 提供一个稳定、高性能的 RUDP 传输。

## 为什么选择 Ignorance 而不是 Unity LLAPI? <a href="#why-ignorance-over-the-unity-llapi" id="why-ignorance-over-the-unity-llapi"></a>

Unity 的旧 LLAPI 效率非常低下，大量测试表明在项目中使用 Unity LLAPI 会导致性能下降。这是由于旧网络代码的设计 - Unity Tech 做出了“有意为之”的决定和糟糕的错误修复，被其他开发人员视为对一个严重问题的临时解决方案。他们并不关心性能或错误修复。

Unity LLAPI 也是闭源的，这意味着 Mirror 的开发人员无法对其进行改进。这就是 Ignorance 概念的由来。

## 谁开发了 Ignorance? <a href="#who-develops-ignorance" id="who-develops-ignorance"></a>

[Coburn](http://github.com/softwareguy) 是这个传输层的首席开发人员。Oiran Studio 积极使用这个传输层进行网络游戏项目。目前一些游戏项目也在使用它，你可以在 Mirror Discord 服务器上找到。

## 为什么我会选择可靠的 UDP 而不是 TCP? <a href="#why-would-i-want-to-use-reliable-udp-over-tcp" id="why-would-i-want-to-use-reliable-udp-over-tcp"></a>

* 如果你需要实时通信，需要速度而不是可靠性（VoIP...）
* 如果你需要通道
* 如果你需要自定义通道发送类型
* 如果你的游戏需要一个数据管道（第一人称射击游戏、赛车游戏等）

## 为什么我不想使用可靠的 UDP 而选择 TCP？(Why wouldn't I want to use reliable UDP over TCP) <a href="#why-wouldnt-i-want-to-use-reliable-udp-over-tcp" id="why-wouldnt-i-want-to-use-reliable-udp-over-tcp"></a>

* 如果你有**至关重要**的事情（比如，数据**必须**从 A 到 B，没有例外）
* 如果你需要完全可靠的网络协议
* 如果你是个偏执狂
* 如果你正在制作类似 Minecraft 的游戏并且需要保持所有人同步

## 我想了解更多关于可靠的 UDP... (I want to know more about reliable UDP) <a href="#i-want-to-know-more-about-reliable-udp" id="i-want-to-know-more-about-reliable-udp"></a>

需要一点解释。UDP 最好被描述为“散弹”数据传输协议，这意味着你只是将数据包喷射出去，然后祈祷它们能到达目的地并且一切顺利。远程目的地可能会收到这些数据包，也可能不会，而且它们也不会按顺序到达。例如，如果你有一个数据包流：

```
1 2 3 4 5 6 7
```

...那么由于数据包乱序到达，它可能在另一端变成以下任何一种情况。以下示例中的点表示该数据包丢失。

```
7 6 1 3 2 4 5
7 6 . . 4 . 1
. . . . 1 2 3
1 2 3 5 4 6 7
```

例如，假设你丢失了一个包含玩家生命值更新的数据包。其他人可能知道他们受到了 69 点伤害，但该客户端仍然保留旧值，比如说，72 点生命值。没有可靠的 UDP，你很快就会失去同步。当你失去同步时，游戏就结束了 - 一切都会开始运行得非常奇怪。

## 排序和可靠交付 (Sequencing and Reliable Delivery) <a href="#sequencing-and-reliable-delivery" id="sequencing-and-reliable-delivery"></a>

### 排序 (Sequencing) <a href="#sequencing" id="sequencing"></a>

**排序** 基本上是给数据包打上标记，以便在发送时知道它们的编号。因此，如果你发送数据包 `100, 101, 102` 到远程目的地，另一端将按照这个顺序重建数据包，而不是按照不同的顺序（比如 `101, 100, 102`）。如果一个数据包丢失，它将被跳过，但网络库会记录下它的丢失并进行补偿。

**可靠** 模式告诉 ENET 在等待远程确认数据包接收之前发送数据包。如果没有从远程收到回应，ENET 仍然会将这些数据包标记为丢失，但会重新发送它们以补偿丢失连接或高延迟情况。可靠模式试图模拟 TCP 在未及时确认时的重发，但由于 UDP 没有 TCP 协议的所有开销，它会增加一些数据包开销。

Ignorance 默认情况下在 Reliable（可靠）和 Unreliable（不可靠） 模式下都有两个通道。开发人员可以测试其他通道模式，因为不同的模式可能适用于不同的负载，但普通用户不需要担心这一点。Ignorance 在开箱即用时具有合理的默认设置。

## Does Ignorance support Websockets?（Ignorance 是否支持 Websockets？） <a href="#does-ignorance-support-websockets" id="does-ignorance-support-websockets"></a>

不支持。Mirror 自带内置的 Websockets 支持。

## Where can I get Ignorance?（我从哪里获取 Ignorance？） <a href="#where-can-i-get-ignorance" id="where-can-i-get-ignorance"></a>

[从 Ignorance 仓库的发布页面获取最新版本](https://github.com/SoftwareGuy/Ignorance)。只需导入您下载的发布版本的 Unity Package。

## Where can I get support?（我从哪里获取支持？） <a href="#where-can-i-get-support" id="where-can-i-get-support"></a>

您可以通过在 [Ignorance 仓库问题跟踪器](https://github.com/SoftwareGuy/Ignorance/issues) 上开启问题工单或在 Mirror Discord 服务器的 #ignorance 频道获取支持。

## I still don't understand what this transport is, my head is spinning, help!（我仍然不明白这个传输是什么，我的头晕了，帮帮我！） <a href="#i-still-dont-understand-what-this-transport-is-my-head-is-spinning-help" id="i-still-dont-understand-what-this-transport-is-my-head-is-spinning-help"></a>

加入 Discord，我们将尽力用简单的英语解释。
