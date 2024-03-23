# 网络变换

网络变换组件同步网络游戏对象的位置、旋转和缩放。

<div align="center">

<figure><img src="../../../.gitbook/assets/image (63).png" alt=""><figcaption><p>网络变换组件</p></figcaption></figure>

</div>

{% hint style="info" %}
Mirror 目前提供**两种** NetworkTransform 变体：

* 可靠（Reliable）：低带宽，与 Rpcs/Cmds 等具有相同的延迟。
* 不可靠（Unreliable）：高带宽，极低延迟

除非需要极低延迟，否则请使用 NetworkTransform**Reliable**。


{% endhint %}



具有网络变换组件的游戏对象还必须具有网络标识（Network Identity）组件。当您向游戏对象添加网络变换组件时，如果该游戏对象尚未具有网络标识组件，Mirror 也会在该游戏对象上添加网络标识组件。

默认情况下，网络变换是服务器授权的，除非您将同步方向更改为客户端到服务器（Client To Server）。客户端权限适用于玩家对象以及已专门分配给客户端的非玩家对象，但仅适用于此组件。启用此功能后，位置更改将从客户端发送到服务器。

您可以使用**同步间隔（Sync Interval）**指定同步频率（以秒为单位）。

### 在延迟、丢包和抖动期间实现平滑移动

`NetworkTransform` 在 `Unreliable` 通道上每 `sendInterval` 发送一次移动更新。

网络条件永远不理想，因此这些更新可能会以无序、延迟或在传输过程中丢失的方式到达。

在非理想的网络条件下实现平滑移动是游戏网络中较难的问题之一。我们的 `NetworkTransform` 组件通过使用[快照插值（Snapshot Interpolation）](snapshot-interpolation.md)来解决这个问题。我们建议阅读链接的章节以深入了解。

简而言之，在非理想条件下实现平滑移动是通过缓冲来实现的。网络条件越糟糕，**缓冲时间倍增器（Buffer Time Multiplier）**就需要越高。然而，它越高，缓冲时间也越长。

总缓冲时间由 `sendInterval * 缓冲时间倍增器(Buffer Time Multiplier)` 计算。通常建议使用因子 '3'。

这意味着，虽然你可以**增加** `缓冲时间倍增器(Buffer Time Multiplier)` 来弥补更糟糕的条件，但你也可以**减少** `sendInterval` 以保持相对较低的缓冲延迟。

我们的新网络变换(Network Transform)已经被几个真实世界的游戏项目使用在生产中。查看[这个视频](https://www.youtube.com/watch?v=z2JpT_qLmzk)来比较我们旧版和新版的**网络变换(Network Transform)**组件。



{% hint style="info" %}
注意：\
在更新的 Mirror 版本中，同步间隔已移至 NetworkManager，称为 "发送速率(Send Rate)"。\
发送速率为 10，将对应 0.1 的同步间隔。
{% endhint %}

