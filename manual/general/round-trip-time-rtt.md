---
description: Mirror 的 RTT 快速概述，以及如何在游戏中改进它。
---

# 往返时间（RTT）

###

### 理解 RTT

{% hint style="info" %}
**往返时间（rtt）** 是消息往返到另一端并返回所需的时间。\
RTT 始终取决于两个因素：

1. **延迟（Latency）:** 网络消息在互联网上传输需要一定时间。
2. **更新间隔（Update Interval）:** 消息需要被处理并发送回另一端。如果服务器的更新频率较慢，RTT 将会较大。
{% endhint %}

Mirror 在客户端和服务器端分别计算往返时间：

* 客户端可以在 `NetworkTime.rtt` 中找到
* 服务器可以在每个 `NetworkServer.connection.rtt` 中找到（每个连接的值不同）

如果你想在游戏中显示 **rtt**，你可以使用我们的 `NetworkPingDisplay` 组件。



### 本地机器的 RTT

如果你在同一台计算机上运行我们的坦克演示，一个作为服务器，一个作为客户端，你会看到平均 rtt 大约为 8 毫秒：

<figure><img src="../../.gitbook/assets/2023-07-18 - rtt 8ms.png" alt=""><figcaption><p>60 Hz 更新频率给出 8 毫秒的 RTT</p></figcaption></figure>

这一开始可能看起来有些奇怪：虽然 8 毫秒并不多，但仍然不像你在本地计算机上期望的那么低。显然，数据包不需要 8 毫秒才能通过内存传输。

原因很简单：默认情况下，Mirror 的 NetworkManager.sendInterval 设置为 60 Hz。

这意味着网络更新每 16 毫秒发生一次（1 秒 / 60）。

RTT 消息可能在更新期间到达，或者在等待下一个更新的 16 毫秒内到达。

平均而言，这意味着每个 RTT 消息有 8 毫秒的等待时间。

这就是为什么上面的图片显示在本地计算机上大约为 8 毫秒的 RTT。

### 优化 RTT

60 Hz 的发送间隔是在带宽（发送频率越高，带宽成本越大）和 RTT 之间的一个合理折衷。如果你想要用更低的 RTT 换取更多的带宽：

* 将 sendInterval 增加到 120 Hz 或更高
* 确保 VSYNC 已禁用（如果你的屏幕运行在 60 Hz，Vsync 将限制你的更新频率为 60 Hz）
* 确保你的游戏运行速度足够快（如果你的 CPU 太慢无法达到 120 Hz，那么你的发送频率显然不能是 120 Hz）

通过极高的发送速率和帧率，我们可以轻松将 Tanks 演示的 RTT 降至 1-2 毫秒在我们的本地机器上。当然，在生产游戏中不建议这样做：

<figure><img src="../../.gitbook/assets/2023-07-18 - rtt 2ms.png" alt=""><figcaption></figcaption></figure>
