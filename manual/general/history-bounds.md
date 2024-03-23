---
description: 优化延迟补偿和客户端预测
---

# 历史边界

在前一章中，我们讨论了如何使用延迟补偿来检查玩家过去的碰撞框。

为了最小化性能开销，在我们决定回滚哪些玩家之前，首先对`HistoryBounds`进行投射可能是有用的。

&#x20;本质上是一个四维边界框：(x,y,z,time)。\
换句话说，一个包含了过去一秒钟左右所有玩家位置的边界框。



{% embed url="https://www.youtube.com/watch?v=LPfbAAIM3LY" %}
橙色的碰撞体显示了坦克在过去一秒钟内的红色碰撞体。
{% endembed %}

### 使用指南:

* 将`HistoryCollider`组件附加到您的NetworkIdentity上
* 确保您的NetworkIdentity有一个碰撞体，将其拖放到组件的`actualCollider`字段中
* 添加一个带有BoxCollider并启用isTrigger的子游戏对象。将其拖放到组件的`boundsCollider`中。
* 按下播放，启用Gizmos，注意橙色的HistoryCollider。
*   该组件会自动调整子触发碰撞体以匹配橙色框，这意味着您可以非常容易地对所有历史碰撞体使用Physics.Raycast。



换句话说：如果一个玩家开枪，做这个：

* 对所有HistoryColliders进行射线投射，找出需要检查的其他玩家
* 然后为碰撞体的父NetworkIdentity应用延迟补偿
* 然后检查是否被击中

{% hint style="info" %}
这是关于为什么存在HistoryCollider以及如何使用它的简要概述。\
在未来，我们将提供一个包含所有高级技术的适当的FPS演示。
{% endhint %}

