---
描述：你需要知道的关于Mirror的新预测算法的一切！
---

# 客户端预测

<figure><img src="../../.gitbook/assets/2024-03-21 - 12-13-11@2x.png" alt=""> <figcaption><p>台球是学习预测的完美例子。</p></figcaption></figure>

**_好消息：_**经过 8 个月的全职工作与游戏工作室合作，镜子现在有一个生产准备**预测**算法！

让我们来看看为什么我们甚至需要预测，它是如何工作的，我们如何实现它，以及你**今天**可以在你的游戏中使用它。x20;

{% hint style="info"%}
请阅读到最后，以了解我们的预测算法的**背景故事**！
联系我们

## 为什么需要预测

第一件事：让我们尝试台球**没有预测**，看看为什么我们需要它！

- 打开**示例/台球**演示
- 确保 NetworkManager -> LatencySimulation 增加一些延迟（50 毫秒）
- 构建它，选择**仅服务器**
- 在编辑器中播放，选择**客户端**连接到您的构建
- 单击并拖动白色球以施加力

你**应该**感觉到从施加力到看到物理反应有明显的**延迟**！

这里强调的是\*"**应该感觉"\***：如果这在你的游戏中不是一个问题，那么你**就不需要预测！**

例如，在 2000 年代有许多流行的 MMO，你只需等待输入，这是好的。 对于纸牌游戏，策略游戏，甚至大多数游戏来说也是如此-通常可以等待 50 毫秒。

然而，对于快节奏的游戏，如射击游戏，VR，甚至是简单的物理游戏，如台球，有一个明显的延迟是完全不可接受的。 正如你可能已经猜到的，有一个解决方案，它被称为预测！

{% hint style="info"%}
预测意味着立即模拟客户端上的操作，以避免延迟。
一旦服务器状态返回，我们将比较预测并在必要时进行更正！
联系我们

如果这个定义让你感到困惑，让我们再详细解释一下。

通常，这是当客户想要对台球施加力时发生的情况：

- 客户端发送`[命令] CmdApplyForce(force)`到服务器（. 这需要 50 ms）
- 服务器执行`Rigidbody.AddForce(force)`
- 服务器将新的刚体位置同步到客户端（. 这需要 50 ms）
- 客户端在 50 ms +50 ms = 100 ms 后看到结果

现在我们可能会想，好吧，让我们立即在客户端上施加力：

- Client 执行`Rigidbody.AddForce(force)`
- 这发生在本地，服务器和其他客户端都看不到：sob：

好的，那么，如果我们在本地应用力并将其同步到服务器：

- Client 执行`Rigidbody.AddForce(force)`
- 客户端发送`[命令] CmdApplyForce(force)`到服务器（. 这需要 50 ms）
- 服务器也执行`Rigidbody.AddForce(force)`
- 每个人都很开心... 是吧？ 错了！ ：disappointed_relieved：

不幸的是，大多数物理引擎（包括 Unity 的 PhysX）都**不是确定性**的。 这意味着在客户端上施加力将在服务器上产生略微不同的结果。 这些差异很快就加起来，大约半秒后，球就完全不同步了。 是啊，真糟糕。 原因是"浮点"操作不是确定性的。 如果我们在两台不同的机器上计算`Rigidbody.position += Vector2.up`，我们会得到稍微不同的结果。 这种差异加起来在不到一秒的时间内就产生了巨大的去同步。

现在你可能会想：为什么我们不使用**确定性物理**引擎呢？ 好吧，因为 Unity 没有一个。 第二，建造一个这样的机器需要很多工作。 第三，它比常规物理学慢，因为我们需要的不是浮点数，而是定点数，这实际上需要两倍的运算。

所以，下一个问题是，解决方案是什么？ 如果客户端应该立即模拟，但客户端和服务器总是漂移，那么我们该怎么办？

最简单的解决办法是这样的：

- Client 执行`Rigidbody.AddForce(force)`
- 客户端发送`[命令] CmdApplyForce(force)`到服务器（. 这需要 50 ms）
- 服务器也执行`Rigidbody.AddForce(force)`
- 服务器将新的刚体位置同步到客户端（. 这需要 50 ms）
- 客户端比较位置和硬纠正自己的模拟，如果必要的。

注意这里有两个... 它需要 50 毫秒"在那里！ 客户端已经移动了，现在它突然得到了 50 + 50 = 100 ms 的服务器状态。 纠正这种状态必须解决以下主要问题：

1. 状态为 100 ms 前。 客户端的当前状态几乎总是已经在其他地方，导致所有的时间修正。
2. 修正会将其设置为 100 ms 前的位置，这意味着球每次都会明显向后跳。

那解决办法是什么

理论上其实很简单。 客户端只需要存储头寸的历史记录：

- Client 执行`Rigidbody.AddForce(force)`&#x20;
- _（客户端每隔 50 ms 保存一次 Rigidbody.position，以便以后进行比较）_
- 客户端发送`[命令] CmdApplyForce(force)`到服务器（. 这需要 50 ms）
- 服务器也执行`Rigidbody.AddForce(force)`
- 服务器将新的刚体位置同步到客户端（. 这需要 50 ms）
- 客户端与刚体 50+50=100 ms 前的位置进行比较！
  - 如果有不匹配的地方，那就做些修正。

因此，如果我们在 50 ms、100 ms 或 150 ms 后获得服务器状态，这并不重要。
客户端只需检查历史记录\[- 100 ms]并进行比较！

可视化后，它可能看起来像这样，其中白框是位置的历史：

<figure><img src="../../.gitbook/assets/2024-03-21 - 13-15-30@2x.png" alt=""> <figcaption></figcaption></figure>

好了，我们快到了。

**最后一个问题是：我们如何进行修正？**

现在我们改变 100 ms 前的位置。 但是我们如何让它对台球现在的位置产生影响呢？\*\*\*\*

好吧，这样如何：我们应用三角洲。 如果球之前是在（1，2，0），并且已经向前移动了一点，向右移动了一点-然后将其纠正为类似于（1.1，2，0）的东西，然后再次将其向前移动一点，然后再向右移动一点。

总结一下：

{% hint style="success"%}
预测的工作原理是**保持**历史，**纠正**过去，并**将**三角洲倒回顶部。
联系我们

如果这一切都没有意义，不用担心。

你不必亲自动手。 像往常一样，镜子照顾一切为你！

不管怎样，让我们在行动中试试吧！

## 预测台球

现在我们了解了什么是预测以及它是如何工作的，让我们现在就尝试一下！

- 在**GitHub**上的最新 Mirror 中打开 Examples/**BilliardsPredicted**示例。
  - 资产商店 Mirror 版本可能有此演示，但截至 2024 年 3 月尚未更新。 请从[GitHub Releases](https://github.com/MirrorNetworking/Mirror/releases)下载。
- 构建它，在构建中选择**Server Only**，然后在 Unity 编辑器中**登录**客户端。
- 单击并拖动白色球以对其应用快照。
- 注意物理学如何**立即**反应，没有任何延迟。
- 尝试将[延迟模拟](../transports/latency-simulaton-transport.md)组件中的延迟增加到 50 毫秒。
- 构建并再次尝试-即使有延迟，反应也是即时的！

<figure><img src="../../.gitbook/assets/2024-03-21 - Predicted Billiards Release.png" alt=""> <figcaption></figcaption></figure>

你注意到那些透明的幽灵物体了吗？ 使用 PredictedRigidbody 时，始终存在：

- **（透明的）被预测的物理对象：**这是刚体，它提前预测并对其应用了校正。
- **（渲染的）**原始对象：这是"你的"原始对象，没有物理学。 这就是玩家所看到的。 它自动跟随物理对象，但应用了一些平滑。
- **（透明的）**远程状态对象：这是用于调试的。 它显示对象的最新服务器状态。

## 将 PredictedRigidbody 添加到游戏中

如果你想在游戏中的网络刚体中添加预测，这非常简单！

**首先**，将**PredictedRigidbody**组件添加到预制件中：

<figure><img src="../../.gitbook/assets/2024-03-21 - 13-48-29@2x.png" alt=""> <figcaption></figcaption></figure>

**第二**，在客户端模拟物理**并**将命令发送到服务器：

```csharp
// handle inputs on client
void HandleClick()
{
    GetComponent<Rigidbody>().AddForce(force); // simulate on client
    CmdAddForce(force); // and let the server know
}
```

**第三**，在服务器上模拟物理**INSIDE**命令：

```csharp
[Command]
void CmdAddForce(Vector3 force)
{
    GetComponent<Rigidbody>().AddForce(force); // simulate on server
}
```

仅此而已！ `PredictedRigidbody`为您处理所有的魔法！

{% hint style="danger"%}
但是等等，还有一件事……
联系我们

还记得 PredictedRigidbody 是如何将你的 Rigidbody+ Collider 临时移动到一个幽灵对象上的吗？

这意味着 GetComponent\<Rigidbody>（）实际上不会一直工作。 ：sob：

但不用担心，只需从我们的 PredictedRigidbody 组件中获取 Rigidbody 即可：

```csharp
// PROPERLY handle inputs on client
void HandleClick()
{
    // Rigidbody access via PredictedRigidbody
    Rigidbody rb = GetComponent<PredictedRigidbody>().predictedRigidbody;
    rb.AddForce(force); // simulate on client
    CmdAddForce(force); // and let the server know
}
```

```csharp
[Command]
void CmdAddForce(Vector3 force)
{
    // on server, this is still fine since Rigidbody always remains on the object
    GetComponent<Rigidbody>().AddForce(force); // simulate on server
}
```

是的，这就是它。你需要的一切就是 4 行代码和 1 个组件！

再重复一次：

重要的是要理解，一旦你添加 PredictedRigidbody 到一个对象，它会自动分离刚体和碰撞器到一个幽灵对象，而预测！

{% hint style="danger"%}
请记住这一点。x20;

`GetComponent()`在预测时并不总是可用。

`GetComponent()`在预测时并不总是可用。

`OnCollisionEnter/Exit()`在预测时不会总是被调用。

`OnTriggerEnter/Exit()`在预测时不会总是被调用。
联系我们

你可能还不需要这个，但是为了将来，如果你想让 OnCollision 回调工作：

```csharp
// for OnCollision, move the code from your predicted object
// to the 'other' object instead and grab the original from the collider:
void OnCollisionEnter(Collider collider)
{
    // check if the collider is from a predicted object
    // (which means it may be on the original, or on the ghost object)
    if (PredictedRigidbody.IsPredicted(collider, out PredictedRigidbody original)
    {
        Debug.Log("Collided with {collider} which belongs to {original}");
    }
}
```

只需检查**PredictedBilliards**示例的代码-它实际上并不太难！

## 预测其他类型

本文重点介绍`PredictedRigidbody`--这是一个完全可用的组件--但仅适用于 Rigidbody。

如果你想预测其他类型，比如 ChronterControllers，有好消息和坏消息：

- **好消息**是，基本的预测和校正算法是通用的。 您可以在 Prediction.cs 中找到它们，并将其用于其他类型。
- **坏消息**是，它们只是独立的算法。 如果你想制作一个像 PredictedController 这样易于使用的组件，那么在它上面还有一些工作要做。x20;

我们建议您查看 PredictedRigidbody.cs 中的代码，以了解高级组件和低级算法之间的分离，并了解预测特定类型（如 Rigidbody）需要多少额外工作。

总结一下：你可以将 Mirror 的预测用于其他类型，但你必须做一些工作。

值得一提的是，最难的部分（算法）可以工作，并且有很大的测试覆盖率！

## 大型物理场景的镜面预测

现在还有最后一个细节，你绝对必须了解镜子的预测。

传统上，预测算法总是回滚和重新模拟**整个物理场景。**
因为如果我们想重新模拟一个`Rigidbody`，显然我们需要`Physics.Simulate()`：

```csharp
// grab simulation 100 ms ago
WorldSnapshot state = history[-100ms];

// compare with server state
if (state != serverState)
{
    // resimulate everything
    state = serverState;
    Physics.Simulate();

    // ... do this again for 50ms ago, 25ms ago, 0ms ago etc.
}
```

虽然这是最**正确**的预测解决方案，但也有一个缺点：性能。

每次校正多次重新模拟大型物理场景是超级昂贵的！

虽然这提供了最好的结果，但它非常占用 CPU，并且不能很好地扩展到大型场景。

这**不是**镜子做的！

{% hint style="success"%}
镜子的预测在没有**`Physics.Simulate()`**。
联系我们

是的，你没看错！ 但没有什么是免费的，所以让我们继续阅读。

Mirror 预测是与一个游戏工作室合作开发的，旨在用数千个预测的刚体构建网络物理场景。 使用**Physics.Simulate（）**从来都不是我们实现的对象，因为它不会扩展，所以我们必须有创造性。

起初我们认为：没有办法将预测扩展到那么多对象。 但有一个陷阱：虽然场景具有数千个预测的刚体，但是在任何给定时间，本地玩家仅与其中的少数刚体交互。 例如，在大多数游戏中，你可能会抓住一个瓶子或踢下梯子，但你很少同时与数千个刚体互动。x20;

虽然有些游戏需要与数千个刚体进行交互（即破坏型游戏），但这里的情况并非如此。 所以我们想：为什么我们不尝试在不使用 PhysX 的情况下手动重新模拟单个刚体！

虽然我们不相信它会起作用，但我们别无选择。 所以，无论如何，我们都要尝试一下。x20;

## 历史

我们从一个非常简单的例子开始，主要是在 2D：预测台球-你可以在你的 Mirror 文件夹中找到今天的例子。

<figure><img src="../../.gitbook/assets/2024-03-21 - 14-46-53@2x.png" alt=""> <figcaption></figcaption></figure>

具体来说，我们尝试在我们的 C#代码中手动重新模拟 Rigidbody.position/rotation/velocity/angularVelocity，在任何物理引擎之外。 这有点工作的头几个月，但它从来没有真正看起来足够好的生产游戏。 但是，我们别无选择，所以我们只能坚持下去。经过 4 个月的调试，我们设法修复了一些错误的计算，不一致和错误的预测。x20;

<figure><img src="../../.gitbook/assets/2024-03-21 - 14-49-29@2x.png" alt=""> <figcaption><p>我们用许多小发明、绘图线和逐帧逐步通过记录来调试错误预测。</p></figcaption></figure>

我们的预测台球演示实际上最终工作得很好-比我们预期的要好得多。 所以是时候把它移植到真正的游戏中了！

... 几乎如预期的那样，一切都打破了，预测在一个更复杂的场景中看起来很糟糕。 但是，我们又一次别无选择，所以我们只能继续调试和痛苦地修复一个又一个问题。 特别是，我们必须添加对所有类型的碰撞器和关节以及子对象上的刚体的支持。 三个月后，出乎所有人的意料，这真的很有效！

不幸的是，我们不能显示游戏的任何视频，但只是总结这一次：

{% hint style="success"%}
Mirror 的预测对于玩家一次只**与几个物体\*\***交互**的**大型物理场景\***\*非常有效**

我们的算法**为了性能牺牲了准确性！**
联系我们

换句话说：它非常适合我们开发的游戏。

它可能适用于你的游戏，也可能不适用，因为我们仍然需要用更复杂的物理来测试它！

## 最差情况基准

Mirror 的预测针对大型物理场景进行了优化，在这些场景中，玩家一次只能与少数对象进行交互。 然而，我们仍然建立了一个最坏情况的基准测试，在那里你可以产生几百个（或几千个）对象，这些对象总是被预测的。x20;

我们使用这个基准测试进行性能分析和性能优化。 欢迎在**Examples/BenchmarkPrediction**中查看，这可能是你能找到的最简单的预测示例！

<figure><img src="../../.gitbook/assets/2024-03-21 - 19-03-23@2x.png" alt=""> <figcaption></figcaption></figure>

## 下一步是什么

在支持可交互对象之后，我们即将实现的两个主要目标是：

- 复杂的物理与堆叠的对象
- 预测球员移动

我们现在正在研究预测的堆叠对象。 截至 2024 年 3 月，它们通常同步良好，但尚未完全停止。 就像早期的台球演示一样，它们看起来还不够好，不适合制作游戏！ 一旦他们工作得很好，我们也会发布一个演示。

{% embed url="https://www.youtube.com/watch? v= WI0oV8 bUo"%}

预测的球员移动还没有经过任何测试。 它可能会起作用，也可能不会起作用，我们很可能需要增加 10%的容忍度：接受 10%的错误预测，而不是总是硬纠正它们。 这是因为对于玩家来说，在让他们做出一点点错误预测的同时，平滑应该是一个值得权衡的权衡，而不是一直纠正。

一旦这个工作顺利，我们将建立一个演示！

{% hint style="info"%}
不使用 Physics。Simulate（）是一种有风险的方法。 当然也有可能我们会遇到一个局部最大值，如果不使用 Physics.Simulate（）就无法进一步改进它。 现在，这不是一个选择。
联系我们

{% hint style="info"%}
预测仍将是我们 2024 年剩余时间的重点。

让我们看看它去哪里，请尝试它，并报告回来！ ：火箭：
联系我们
