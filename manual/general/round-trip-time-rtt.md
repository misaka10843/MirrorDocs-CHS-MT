---
描述：快速概述Mirror的RTT，以及如何在游戏中改进它。
---

# 往返时间(RTT)

###

### 了解 RTT

{% hint style="info"%}
**往返时间(rtt)**是消息往返于另一端所花费的时间。
RTT 始终取决于两个因素：

1. **延迟：**网络消息在 Internet 上传输需要一些时间。
2. **更新间隔：**消息需要被处理并发送回另一端。 如果你的服务器更新速度很慢，RTT 就会很大。
   联系我们

Mirror 计算客户端和服务器上的往返时间：

- 客户端可以在`NetworkTime.rtt`中找到它
- 服务器可以在每个`NetworkServer.connection.rtt`中找到它(每个连接不同)

如果你想在游戏中显示**rtt**，你可以使用我们`NetworkPingDisplay`组件。

### 本地机器 RTT

如果您在同一台计算机上运行我们的 Tanks 演示，服务器和客户端，您将看到平均 rtt 约为 8 ms：

<figure><img src="../../.gitbook/assets/2023-07-18 - rtt 8ms.png" alt=""> <figcaption><p>60 Hz更新速率提供8 ms RTT</p></figcaption></figure>

这可能看起来很奇怪：虽然 8 毫秒并不多，但它仍然没有您在本地计算机上期望的那么低。 显然，数据包在你的记忆中不需要 8 毫秒。

原因很简单：默认情况下，Mirror 的 NetworkManager.sendInterval 设置为 60 Hz。

这意味着网络更新每 16 毫秒(1 秒/ 60)发生一次。

RTT 消息可能在更新期间到达，或者在等待下一次更新的 16 ms 时到达。

平均而言，这意味着每个 RTT 消息有 8 毫秒的等待时间。

这就是为什么上面的图片显示在您的本地机器上的 RTT 约为 8 ms。

### 优化 RTT

60 Hz 的发送间隔是带宽(发送越频繁，带宽成本越大)和 RTT 之间的合理权衡。 如果您想以更高的带宽换取更低的 RTT：

- 将 sendInterval 增加到 120 Hz 或更高
- 确保 VSYNC 已禁用(如果您的屏幕以 60 Hz 运行，则 VSYNC 将更新速率限制为 60 Hz)
- 确保你的游戏运行速度足够快(如果你的 CPU 太慢，无法达到 120 Hz，那么你的发送速率显然不能是 120 Hz)

凭借极端的发送速率和帧速率，我们可以轻松地在本地机器上将 Tanks 演示的 RTT 降低到 1- 2 ms。 当然，这并不推荐在制作游戏中使用：

<figure><img src="../../.gitbook/assets/2023-07-18 - rtt 2ms.png" alt=""> <figcaption></figcaption></figure>
