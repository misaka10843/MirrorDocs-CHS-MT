# 快照插值

Mirror的`NetworkTransform`组件使用[**快照插值**](https://gafferongames.com/post/snapshot\_interpolation/)。

{% hint style="info" %}
如果您对术语不熟悉：**快照插值**通过快照进行插值，同时缓冲足够多的快照来弥补网络条件不佳，如延迟、丢包和混乱。
{% endhint %}

在开发新的`NetworkTransform`组件时，我们有两个目标：

- **使其稳定**：我们需要一个可以被成千上万个项目使用而没有任何意外的东西。主要游戏已经使用Mirror并投入生产，因此我们需要非常小心地做对这件事。
- **使其可重用**：NetworkTransform是许多需要快照插值的组件之一。有人可能需要它用于3D / 2D刚体、角色控制器等。但算法始终是相同的。

为了实现这两个目标，我们决定将**快照插值**算法拆分为一个独立的类，任何人都可以使用它。它是原始的C#，完全独立于Mirror和Unity。您可以在Mirror中使用它，也可以在独立服务器或不同游戏引擎中使用它。

简而言之，我们希望一劳永逸地**解决问题**，这样我们就有一个算法可以在未来几十年中使用，无论使用哪个引擎。

{% hint style="info" %}
这种方法类似于**kcp**，它只是一个可用于所有语言和引擎的可靠性算法。对于我们的kcp传输，我们只需将其包装在服务器和客户端类中，并通过UDP套接字发送结果。
{% endhint %}

## 模拟与测试

**快照插值**非常难以做到正确。我们不仅要在两个时间轴（本地和远程）上操作，还必须处理不利的网络条件，如延迟突增、丢包和混乱。更糟糕的是，服务器和客户端有时可能也承受着沉重的负载，并且更新频率可能明显不同。

{% hint style="info" %}
为了让事情明朗：快照插值算法花费我们**4个月**的时间来完善。超过一半的时间用于测试和模拟以确保稳定性。
{% endhint %}

开发快照插值作为一个独立的算法，使我们能够模拟不同的场景，甚至无需运行延迟模拟或Mirror：

- 我们可以模拟快照插值算法在远程处于 t=5，我们处于 t=3，并且在 t=0, 1, 2 有三个快照的情况下，通过不同的插值点进行模拟。
- 我们可以模拟如果有人只有两个快照，到达插值的末尾，仍在等待下一个快照的情况。
- 我们可以模拟移动用户在延迟 100 秒后重新回到游戏的算法行为。
- 我们可以模拟极端恶劣的网络条件，如 99% 的丢包和乱序。
- 如果缓冲区变得过大，我们可以进行追赶。
- 还有许多其他情况...

![快照插值模拟](../../../.gitbook/assets/2021-07-06\_20-23-58@2x.png)

**作为结果**

_这只是一个计算快照和参数，得到结果的过程。_ 预期

## 使用该算法

请阅读我们在 Mirror 中的 `SnapshotInterpolation.cs` 代码，以查看算法和辅助函数，然后阅读 `NetworkTransform` 作为使用示例。

总结一下，有几个关键方面：

- **快照** **接口**：你的 `RigidbodySnapshot`、`CharacterControllerSnapshot` 等结构体必须实现该接口。
- **时间缓冲区**，这是一个 `SortedList<timestamp, Snapshot>` 缓冲区。我们提供了几个辅助函数，如 `InsertIfNewEnough` 以方便使用。
  - 所有这些函数都经过了大量的单元测试。
- **Compute()** 算法：给定一个快照缓冲区、时间、deltaTime 和配置参数，它会输出下一个插值的快照（如果有的话）。
  - 这个函数经过了大量的测试覆盖，事实上，这可能是 Mirror 中所有函数中测试最多的函数。

{% hint style="success" %}
确保阅读 [快照插值](https://gafferongames.com/post/snapshot\_interpolation/) 文章，以了解它是如何工作的，然后查看 `SnapshotInterpolation.cs` 和 `NetworkTransformBase.cs`，看看它是如何运作的。即使有了我们提供的算法，理解和正确实现它仍然不是一件容易的事情。
{% endhint %}

{% hint style="warning" %}
请注意，`NetworkTransform` 每隔`sendInterval`通过**不可靠**通道发送快照。不要仅在**发生更改时**发送，这将需要了解另一端上次接收到的快照（无论是通过**可靠**方式，还是使用**通知**算法）。
{% endhint %}

{% hint style="info" %}
请注意，NetworkTransform 每隔`sendInterval`发送一次。一旦我们将**Bitpacking**和**Delta Compression**引入 Mirror，带宽将显着减少。
{% endhint %}

## 基准测试和结果

我们建议使用 Mirror 的**LatencySimulationTransport**自行尝试，例如使用我们的**Benchmark**演示。即使在网络条件较差的情况下，只要**bufferMultiplier**足够高，快照插值的性能就会很好。

#### 一些测试视频：

* NetworkTransform [旧版与新版比较](https://youtu.be/z2JpT\_qLmzk) 在 Youtube 上（秘密项目）。
* 查看原始[Pull Request](https://github.com/vis2k/Mirror/pull/2791)进度视频。您可以看到每添加一个功能，延迟、丢包和混乱是如何逐渐解决的。
* JesusLovsYooh 旧版与新版 NetworkTransform _(观看左侧版本，OG NT 是旧版，NT 2k 是新版)_

{% embed url="https://www.youtube.com/watch?v=fu7w9vlG7yQ" %}
跳到一半查看在糟糕网络条件下的新 NT。
