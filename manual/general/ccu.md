---
description: 'Mirror 可以处理多少个 CCU 是最常被问到的问题之一？'
---

# CCU（同时在线用户数 Concurrent Concurrent Users）

不久前在[Unity论坛](https://forum.unity.com/threads/stress-test-using-unity-as-server.1126640/#post-7245842)上出现了一个类似的问题，所以我会把我的答案复制到这里，以防对其他人有用。

![我们在2019年进行的延迟480个CCU测试](../../.gitbook/assets/2021-06-17\_12-24-46@2x.png)

一个使用[Mirror 制作的 MMO](https://github.com/vis2k/Mirror#made-with-mirror)在推出时有相当多的 CCU，我想是 Inferna。\
他们将地图分割成单独的服务器实例，每个地图约有200个CCU的限制。\
我需要再查一下，但我相信他们有时以这种方式实现了每个世界约1000个CCU。\
\
还有一个[非常古老的视频](https://www.youtube.com/watch?v=mDCNff1S9ZU\&t=58s)，我们尝试了480个CCU的最坏情况，所有玩家都在一个地方，就像你的视频一样。虽然延迟很严重，但服务器表现得很好。\
\
Inferna 和 480 个 CCU 视频都使用了旧版的 Mirror 和 Unity。自那时以来，Mirror、Unity 和服务器硬件都经过了多年的改进。\
\
我们仍然有很多优化可以从 Mirror 中挤出来，计划在今年年底左右进行另一次 CCU 测试。\
\
请记住，游戏的复杂性也是一个巨大因素。像魔兽世界这样的 3D 物理运动 MMO 比 2D 点击移动 MMO 更难扩展。对于独立开发者来说，2D 确实值得考虑。它更便宜，更容易制作，并且由于物理/网格等较少复杂，扩展性更好。\
\
&#x20;最终，我们在 MonoBehaviour 世界中肯定有所限制。\
&#x20;对于像魔兽世界中的 1500 个物理运动玩家，您肯定需要 DOTS 或者一个不在 Unity 中运行的服务器。\
\
&#x20;在我看来，Unity 和 MonoBehaviour 仍然是一个不错的选择。即使是一个有 500 个 CCU，每个实例有 250 个 CCU 的 MMO，也比永远不发布一个 1500 个 CCU 的 MMO 要好。
