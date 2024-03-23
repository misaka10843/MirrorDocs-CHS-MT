---
描述：延迟补偿和客户端预测的优化
---

# 历史界限

在前一章中，我们讨论了如何使用滞后补偿来检查玩家的命中框。

为了最小化性能开销，在我们决定回滚哪些玩家之前，首先对`HistoryBounds`转换可能很有用。

&#x20;基本上是一个四维边界框：（x，y，z，time）。
换句话说，一个边界框包含了球员在最后一秒左右的所有位置。

{% embed url="https://www.youtube.com/watch? v=LPfbAAIM3LY"%}
橙色的碰撞器显示坦克的红色碰撞器在最后一秒。
联系我们

### 使用指南：

- 将`HistoryCollider`组件附加到 NetworkIdentity
- 确保您的 NetworkIdentity 有一个碰撞器，将其拖到组件的`actualCollider`字段中
- 在 BoxCollider 和 isTrigger 中添加一个子游戏对象。 将其拖到组件的`boundsCollider`中。
- 按下播放，启用 Gizmos，注意橙色的 HistoryCollider。
- 组件自动调整子触发碰撞器以匹配橙色框，这意味着您可以非常轻松地使用 Physics.Raycast 对抗所有历史碰撞器。

换句话说：如果一个玩家开枪，这样做：

- 对所有 HistoryCollider 进行光线投射，以找出我们需要检查的其他玩家
- 然后为碰撞器的父 NetworkIdentity 应用滞后补偿
- 那就查一下有没有被击中

{% hint style="info"%}
这是一个简要概述为什么历史碰撞器存在，以及如何使用它。
在未来，我们将提供一个适当的 FPS 演示与所有先进的技术。
联系我们
