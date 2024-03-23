---
description: Mirror的新预测算法的一切你需要知道！
---

# 客户端预测

<figure><img src="../../.gitbook/assets/2024-03-21 - 12-13-11@2x.png" alt=""><figcaption><p>台球是学习预测的完美例子。</p></figcaption></figure>

_**好消息：**_ 在与一家游戏工作室全职合作 8 个月后，Mirror 现在拥有一个可投入生产的**预测**算法！

让我们来看看为什么我们需要预测，它是如何工作的，我们如何实现它以及你如何在你的游戏中**今天**使用它。&#x20;

{% hint style="info" %}
请阅读到最后了解我们的预测算法的**背景故事**！
{% endhint %}

## 为什么我们需要预测

首先，让我们尝试在没有预测的情况下玩台球，看看为什么我们需要它！

- 打开**Examples/Billiards**演示
- 确保 NetworkManager -> LatencySimulation 添加一些延迟（50ms）
- 构建它，选择**仅服务器**
- 在编辑器中播放，选择**客户端**连接到您的构建
- 点击并拖动白球施加力量

您**应该**感觉到从施加力量到看到物理反应有明显的**延迟**！

这里强调的是*"**应该感觉到"***：如果在您的游戏中这不是问题，那么您**不需要预测**！

例如，在 2000 年代有许多流行的 MMO 游戏，您只需等待输入，这是可以接受的。对于纸牌游戏、策略游戏，甚至大多数游戏来说，等待 50ms 通常是可以接受的。

然而 - 对于像射击游戏、VR，甚至简单的物理游戏如台球这样的快节奏游戏，有明显的延迟可能是完全不可接受的。正如您可能已经猜到的那样，有一个解决方案，它被称为预测！

{% hint style="info" %}
预测意味着立即在客户端上模拟动作以避免延迟。\
一旦服务器状态返回，我们比较预测并在必要时进行更正！
{% endhint %}

如果这个定义让您困惑，让我们再详细解释一次。

通常，当客户端想要向台球球施加力量时，会发生以下情况：

- 客户端发送 `[Command] CmdApplyForce(force)` 给服务器（... 这需要 50 毫秒）
- 服务器执行 `Rigidbody.AddForce(force)`
- 服务器将新的 Rigidbody 位置同步给客户端（... 这需要 50 毫秒）
- 客户端看到结果，这里是 50 毫秒 + 50 毫秒 = 100 毫秒后

现在我们可能会想，好吧，让我们立即在客户端应用力量：

- 客户端执行 `Rigidbody.AddForce(force)`
- 这在本地发生，服务器或其他客户端都看不到 :sob:

好吧，那么，如果我们在本地应用力量并将其同步到服务器呢：

- 客户端执行 `Rigidbody.AddForce(force)`
- 客户端发送 `[Command] CmdApplyForce(force)` 给服务器（... 这需要 50 毫秒）
- 服务器也执行 `Rigidbody.AddForce(force)`
- 每个人都开心... 对吗？错！ :disappointed_relieved:

不幸的是，大多数物理引擎（包括 Unity 的 PhysX）都**不是确定性的**。这意味着在客户端应用力量会在服务器上产生略有不同的结果。这些差异很快累积到一半秒钟后球完全不同步。是的，这很糟糕。造成这种情况的原因是'浮点'运算不是确定性的。如果我们在两台不同的机器上计算 `Rigidbody.position += Vector2.up`，我们会得到略有不同的结果。这种差异在不到一秒钟内就会累积成巨大的不同步。

现在你可能会想：为什么我们不使用**确定性物理**引擎呢？首先，因为 Unity 没有这样的引擎。其次，构建一个确定性物理引擎需要大量工作。第三，它比常规物理慢，因为我们需要固定点数而不是浮点数，这实际上需要两倍的操作。

那么，下一个问题是，解决方案是什么呢？如果客户端应该立即模拟但客户端和服务器总是分离，那么我们该怎么办呢？

嗯，最简单的解决方案是这样的：

- 客户端执行 `Rigidbody.AddForce(force)`
- 客户端发送 `[Command] CmdApplyForce(force)` 给服务器（... 这需要 50 毫秒）
- 服务器也执行 `Rigidbody.AddForce(force)`
- 服务器将新的 Rigidbody 位置同步给客户端（... 这需要 50 毫秒）
- 客户端比较位置，如果有必要，则进行硬校正自己的模拟。

注意一下，里面有两个"... it takes 50 ms"！客户端已经继续前进，现在突然收到了 50 + 50 = 100 毫秒前的服务器状态。对这种状态进行校正会有两个主要问题：

1. 状态是 100 毫秒前的。客户端当前的状态几乎总是在其他地方，导致频繁进行校正。
2. 校正会将其设置为 100 毫秒前的位置，这意味着每次球都会明显向后跳跃。

那么，解决方案是什么呢？

实际上，在理论上非常简单。客户端只需要存储位置的历史记录：

- 客户端执行 `Rigidbody.AddForce(force)`&#x20;
- _(客户端每隔 50 毫秒保存 Rigidbody.position 以供稍后比较)_
- 客户端发送 `[Command] CmdApplyForce(force)` 到服务器（... 这需要 50 毫秒）
- 服务器也执行 `Rigidbody.AddForce(force)`
- 服务器将新的 Rigidbody 位置同步到客户端（... 这需要 50 毫秒）
- 客户端与 50+50=100 毫秒前的 Rigidbody 位置进行比较！
  - 如果不匹配，则进行校正。

因此，无论我们是在 50 毫秒、100 毫秒还是 150 毫秒后获得服务器状态，都没有关系。\
客户端只需在历史记录中检查 \[- 100 毫秒] 并进行比较！

可视化地，可能看起来像这样，其中白色框表示位置的历史记录：

<figure><img src="../../.gitbook/assets/2024-03-21 - 13-15-30@2x.png" alt=""><figcaption></figcaption></figure>

好了，我们差不多到这里了。

**最后一个问题是：我们如何进行校正？**

目前我们改变的是 100 毫秒前的位置。但是如何让它对当前的台球位置产生影响呢？

那么，我们可以这样做：应用增量。如果球之前在 (1,2,0)，然后稍微向前移动和稍微向右移动 - 那么将其校正为类似 (1.1, 2, 0)，然后再次在其上方向前移动和向右移动。

总结一下：

{% hint style="success" %}
预测通过**保留**历史记录，**校正**过去和在其上**回退**增量来实现。
{% endhint %}

## 预测台球

现在我们了解了预测是什么以及它是如何工作的，让我们立即尝试一下吧！

- 打开最新 Mirror 在 **GitHub** 上的示例中的 **BilliardsPredicted** 示例。
  - Asset Store 上的 Mirror 版本可能包含此演示，但截至 2024 年 3 月，它尚未更新。请从 [GitHub Releases](https://github.com/MirrorNetworking/Mirror/releases) 下载。
- 构建它，在构建中选择 **仅服务器**，并在 Unity 编辑器中 **登录** 客户端。
- 点击并拖动白球以进行击球。
- 注意物理反应是**即时**的，没有任何延迟。
- 尝试将我们的 [延迟模拟](../transports/latency-simulaton-transport.md) 组件中的延迟增加到 50 毫秒。
- 再次构建并尝试 - 即使有延迟，反应也是即时的！

<figure><img src="../../.gitbook/assets/2024-03-21 - Predicted Billiards Release.png" alt=""><figcaption></figcaption></figure>

你注意到透明的幽灵对象了吗？在使用 PredictedRigidbody 时，总是有：

- **（透明的）预测物理对象：** 这是预测前进并对其进行校正的 Rigidbody。
- **（渲染的）原始对象：** 这是 '你的' 原始对象没有物理。这是玩家看到的。它会自动跟随物理对象，但会应用一些平滑处理。
- **（透明的）远程状态对象：** 这是用于调试的。它显示对象的最新服务器状态。

## 将 PredictedRigidbody 添加到你的游戏中

如果你想在游戏中为网络化的 Rigidbodies 添加预测，这非常容易！

**首先**，将 **PredictedRigidbody** 组件添加到你的预制体中：

<figure><img src="../../.gitbook/assets/2024-03-21 - 13-48-29@2x.png" alt=""><figcaption></figcaption></figure>

**其次**，在客户端模拟物理**并**将命令发送到服务器：

```csharp
// handle inputs on client
void HandleClick()
{
    GetComponent<Rigidbody>().AddForce(force); // simulate on client
    CmdAddForce(force); // and let the server know
}
```

**第三步**，在命令中**内部**模拟服务器上的物理：

```csharp
[Command]
void CmdAddForce(Vector3 force)
{
    GetComponent<Rigidbody>().AddForce(force); // simulate on server
}
```

就是这样！`PredictedRigidbody` 会为您处理所有的魔法！

{% hint style="danger" %}
但是，请等一下，还有 _一件事_...
{% endhint %}

记得 `PredictedRigidbody` 如何将您的 Rigidbody+Colliders 暂时移动到幽灵对象上吗？

这意味着 `GetComponent\<Rigidbody>()` 并不总是有效。😢

但别担心，只需从我们的 PredictedRigidbody 组件中获取 Rigidbody：

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

是的，这就是全部。您只需要 4 行代码和 1 个组件！

再重复一次：

重要的是要理解，一旦将 PredictedRigidbody 添加到对象中，它将在预测时自动将 Rigidbody 和 Colliders 分离到一个幽灵对象中！

{% hint style="danger" %}
请记住这一点。&#x20;

`GetComponent<Rigidbody>()` 在预测时并不总是可用。

`GetComponent<Collider>()` 在预测时并不总是可用。

`OnCollisionEnter/Exit()` 在预测时并不总是被调用。

`OnTriggerEnter/Exit()` 在预测时并不总是被调用。
{% endhint %}

也许您现在不需要这个，但是以后如果您想让 OnCollision 回调正常工作：

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

只需查看 **PredictedBilliards** 示例的代码 - 实际上并不太困难！

## 预测其他类型

本文重点介绍 `PredictedRigidbody` - 这是一个完整的组件，只适用于 Rigidbodies。

如果您想预测其他类型，比如 CharacterControllers，有好消息和坏消息：

- **好消息** 是底层的预测和校正算法是通用的。您可以在 Prediction.cs 中找到它们，并轻松地用于其他类型。
- **坏消息** 是它们只是独立的算法。如果您想要制作一个易于使用的组件，比如 PredictedCharacterController，那么还需要额外的工作。&#x20;

我们建议您查看 PredictedRigidbody.cs 中的代码，以了解高级组件和低级算法之间的分离程度，并了解预测特定类型（如 Rigidbody）需要多少额外工作。

总结一下：您**可以**使用 Mirror 的预测功能来处理其他类型，但您**将**需要做一些工作。

值得一提的是，困难部分（算法）已经实现并且有很好的测试覆盖率！

## Mirror 的大型物理场景预测

现在有一个您绝对必须了解的最后一个细节关于 Mirror 的预测。

传统上，预测算法总是会回滚并重新模拟**整个物理场景**。\
因为如果我们想要重新模拟一个 `Rigidbody`，显然我们需要 `Physics.Simulate()`：

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

虽然这是预测的最**正确**解决方案，但有一个缺点：性能。

在每次校正时多次重新模拟大型物理场景非常昂贵！

虽然这提供了最佳结果，但它对 CPU 的负荷很重，而且在大型场景中无法很好地扩展。

这**不是** Mirror 的做法！

{% hint style="success" %}
Mirror 的预测在没有**`Physics.Simulate()`**的情况下运行。
{% endhint %}

是的，您没看错！但是没有免费的午餐，所以让我们先继续阅读...

Mirror 的预测是与一个旨在构建具有数千个预测 Rigidbodies 的网络物理场景的游戏工作室合作开发的。由于使用 **Physics.Simulate()** 不具备可扩展性，因此我们的实现从未考虑过这一点，因此我们必须寻找创造性的解决方案。

起初我们认为：没有办法将预测扩展到那么多对象。但有一个问题：虽然场景中有数千个预测的 Rigidbodies，但本地玩家在任何给定时间只与其中的少数进行交互。例如，在大多数游戏中，您可能会拿起一个瓶子或者踢倒梯子，但您很少同时与数千个 Rigidbodies 进行交互。&#x20;

虽然有些游戏需要与数千个 Rigidbodies 进行交互（例如破坏类游戏），但这里并非如此。因此我们想：为什么不尝试手动重新模拟单个 Rigidbodies 而不使用 PhysX！

虽然我们并不相信它会起作用，但我们别无选择。因此，我们决定尝试一下。&#x20;

## 历史

我们从一个非常简化的示例开始，主要是在 2D 中：预测台球 - 你今天可以在 Mirror 文件夹中找到的示例。

<figure><img src="../../.gitbook/assets/2024-03-21 - 14-46-53@2x.png" alt=""><figcaption></figcaption></figure>

具体来说，我们尝试在我们的 C# 代码中手动重新模拟 Rigidbody.position/rotation/velocity/angularVelocity，而不依赖于任何物理引擎。在最初的几个月里，这种方法有点奏效，但从未达到制作游戏的标准。但再次强调 - 我们别无选择，所以我们继续努力。经过 4 个月的调试，我们设法修复了一些错误计算、不一致性和错误预测。&#x20;

<figure><img src="../../.gitbook/assets/2024-03-21 - 14-49-29@2x.png" alt=""><figcaption><p>我们使用大量的 Gizmos、Debug.DrawLine 和逐帧回放来调试错误预测。</p></figcaption></figure>

我们的预测台球演示实际上效果相当不错 - 比我们预期的要好得多。因此，是时候将其移植到一个真正的游戏中了！

... 几乎可以预料到的是，一切都崩溃了，在更复杂的场景中预测看起来很糟糕。但再次强调，我们别无选择，所以我们继续调试并痛苦地修复一个又一个问题。特别是，我们不得不为所有类型的碰撞体和连接器以及子对象上的刚体添加支持。三个月后 - 令所有人惊讶的是 - 这实际上效果非常好！

不幸的是，我们无法展示游戏的任何视频，但再次总结一下：

{% hint style="success" %}
Mirror 的预测在**大型物理场景**中效果非常**好**，玩家一次只**与少数对象**进行**交互**。

我们的算法为性能**牺牲了准确性！**
{% endhint %}

换句话说：它非常适用于我们为之开发的游戏。

它可能适用于你的游戏，也可能不适用，因为我们仍需要在更复杂的物理环境中进行测试！

## 最坏情况基准测试

Mirror 的预测针对大型物理场景进行了优化，其中玩家一次只与少数对象交互。然而，我们仍然建立了一个最坏情况基准测试，您可以在其中生成几百（或数千）个对象，这些对象始终被预测。

我们正在使用此基准测试进行性能分析和优化。请随时在**Examples/BenchmarkPrediction**中查看，这可能是您找到的最简单的预测示例！

<figure><img src="../../.gitbook/assets/2024-03-21 - 19-03-23@2x.png" alt=""><figcaption></figcaption></figure>

## 接下来的计划

在支持可交互对象之后，我们的两个主要目标是：

- 堆叠对象的复杂物理
- 预测玩家移动

我们目前正在致力于预测堆叠对象。截至 2024 年 3 月，它们通常同步良好，但尚未正确停止。就像早期的台球演示一样，它们还不够适合生产游戏！一旦它们运行良好，我们也会发布一个演示！

{% embed url="https://www.youtube.com/watch?v=WI0oV8ZSbUo" %}

预测玩家移动尚未经过任何测试。可能会工作，也可能不会，我们很可能需要添加一个 10%的容差来说：接受 10%的错误预测，而不是始终进行硬校正。这是因为对于玩家来说，平滑性同时让他们稍微错误预测应该是一个值得的权衡，而不是始终进行校正。

一旦这个功能运行良好，我们将构建一个演示！

{% hint style="info" %}
不使用 Physics.Simulate()是一种冒险的方法。很可能我们会达到一个局部最大值，并且无法在不使用 Physics.Simulate()的情况下进一步改进。目前，这不是一个选择。
{% endhint %}

{% hint style="info" %}
预测将是我们 2024 年剩余时间的重点。

让我们看看它的发展，尝试一下并反馈！:rocket:
{% endhint %}
