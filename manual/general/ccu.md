---
描述："最常被问到的问题之一：Mirror可以处理多少CCU？'
---

# CCU

不久前[，Unity 论坛](https://forum.unity.com/threads/stress-test-using-unity-as-server.1126640/#post-7245842)上出现了一个类似的问题，所以我将在这里复制我的答案，以防对其他人有用。

![我们从2019年开始的滞后480 CCU测试](../../.gitbook/assets/2021-06-17_12-24-46@2x.png)

其中一个[用 Mirror 制作](https://github.com/vis2k/Mirror#made-with-mirror)的 MMO 在推出时有很多 CCU，我想是 Inferna。
他们将地图拆分为单独的服务器实例，每个地图限制约 200 个 CCU。
我不得不再次查找它，但我相信他们有时会以这种方式实现每个世界约 1000 CCU。
\
还有一个[非常旧的视频](https://www.youtube.com/watch?v=mDCNff1S9ZU&t=58s)，我们尝试 480 CCU 最坏的情况下，都在一个地方像你的视频。 它是落后的地狱，但服务器生存得很好。
\
Inferna 和 480 CCU 视频都使用旧的 Mirror 和 Unity 版本。 从那时起，我们已经进行了多年的改进，包括 Mirror、Unity 和服务器硬件。
\
我们仍然有很多优化可以挤出 Mirror，另一个 CCU 测试计划在今年年底左右进行。
\
请记住，游戏的复杂性也是一个重要因素。 像魔兽世界这样的 3D 物理运动 MMO 将比 2D 点击移动 MMO 更难缩放。 对于一个独立开发者来说，2D 是非常值得考虑的。 它更便宜，更容易制作，并且由于不太复杂的物理/网格等而更好地扩展。
\
&#x20;在一天结束的时候，我们在 MonoBehaviour 世界中所能达到的目标肯定是有限的。
&#x20;对于 1500 个物理运动玩家来说，你肯定需要 DOTS 或一个不在 Unity 中运行的服务器。
\
&#x20;Imho Unity & MonoBehaviour 仍然是一个不错的选择。 最好是**发布**一个 500 CCU 的 MMO，每个实例 250 CCU，而不是**永远不发布**一个 1500 CCU 的 MMO。
