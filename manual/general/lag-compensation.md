---
描述：了解快节奏游戏的Mirror滞后补偿。
---

# 滞后补偿

滞后补偿（又名回滚）是需要为快节奏/第一人称射击游戏。

对于 MMORPG，纸牌游戏，回合制游戏，策略游戏等，这并不有趣。

## 为什么延迟补偿

让我们来看看为什么游戏需要延迟补偿的最常见的原因。

第一人称射击游戏，如《反恐精英》。

服务器上有两个玩家：你和你的朋友。

服务器会将您朋友的位置发送给您。 它需要**50 毫秒**。

你使用 Mirror`[命令] CmdFire(Vector3 direction)`向你的朋友开火。

`[命令]`需要另外**50 ms**才能到达服务器。

然后服务器会检查你是否击中了你的朋友，并施加伤害。

{% hint style="danger"%}
在过去的**100 毫秒**里，你的朋友已经向左移动了两步。

结果，你总是会**错过投篮**。
联系我们

<figure><img src="../../.gitbook/assets/image (71).png" alt=""> <figcaption><p>你看到你的朋友，你的朋友实际上在哪里。 图片来自<a href="https://developer.valvesoftware.com/wiki/Source_Multiplayer_Networking">Valve的文章</a>。</p></figcaption></figure>

这就是为什么你需要为高节奏/第一人称射击游戏的滞后补偿。

否则，玩家将永远需要瞄准他们的目标。

{% hint style="info"%}
在纸上，这听起来不像是一件大事。
在练习中，玩家会立即抱怨他们没有击中目标。

延迟补偿开启/关闭之间的差异对每个人来说都非常明显。
联系我们

如需更深入的了解，请阅读[Valve 的 Source Multiplayer Networking 文章](https://developer.valvesoftware.com/wiki/Source_Multiplayer_Networking)。 这是一个很好的阅读，值得花时间，即使你还没有完全理解它。

## 用反射镜进行滞后补偿

我们的延迟补偿分为两部分。

#### LagCompensation.cs 独立算法

首先，有一个独立的，Unity 独立的，C# LagCompensation.cs 算法，具有完整的测试覆盖率：

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-26-07@2x.png" alt="" width="344"> <figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-28-43@2x.png" alt=""> <figcaption></figcaption></figure>

该算法可以记录和采样任何类型的历史。
换句话说，如果你愿意，你可以根据自己的需要定制它。
请记住，这是非常低级别的，使用高级组件更容易！

#### 滞后补偿器组件

第二部分：我们`Lag Compensator`组件：

<figure><img src="../../.gitbook/assets/image (146).png" alt=""> <figcaption></figcaption></figure>

这将所有魔法隐藏在一个组件中，该组件会自动记录 3D 中的 Collider 快照历史。 只需将其添加到您的播放器对象，它将为您处理一切！

接下来，找到你的球员在当地发射武器的地方。 应该有一个类似 CmdFireWeapon（）的\[Command]，它将输入发送到服务器。 这就是我们不检查一个球员是否击中了另一个球员，而是首先要求滞后补偿另一个球员在球员射门时的位置。

您可以查看我们的 Shooter 示例来查看实际操作，它就像这样简单：

```csharp
[Client]
void FireWeapon()
{
    // show muzzle flash, play sound etc. locally
    // ...

    // check if anything was hit
    if (Physics.Raycast(camera.position, camera.position + camera.forward, out RaycastHit hit))
    {
        Player target = hit.collider.GetComponent<Player>();

        // notify the server
        CmdFiredWeapon(target, camera.position, hit.position);
    }
}

[Command]
void CmdFiredWeapon(Player target, Vector3 originPoint, Vector3 hitPoint)
{
    // check ammo etc.
    // ...

    // 'this' fired at 'target'.
    // we need to grab 'target's LagCompensator to find out where it was.
    LagCompensator compensator = target.GetComponent<LagCompensator>();

    // next, we can use either of the compensator's 'Check...' functions.
    // viewer is the one who fired, so it's 'this' object.
    // Lag Compensator checks 'this' object's latency and then rewinds 'target's history by that time.
    if (compensator.RaycastCheck(this.connectionToClient, originPoint, hitPoint))
    {
         // valid hit!
    }
}
```

请注意，上面的代码稍微简化了一些，但这就是它的全部内容，除了 LagCompensator 的"Check"函数中的一些可选配置参数。

{% hint style="info"%}
我们的滞后补偿器非常易于使用，如果需要，您仍然可以添加更复杂的检查。 重要的是，我们的独立 LagCompensation.cs 算法是通用的，可以处理你需要的任何东西（2D，3D，自定义物理等）。
联系我们

{% hint style="success"%}
我们的 Mirror 射手演示使用滞后补偿器。
截至 2024 年 2 月，它几乎已经完成，很快就会发布！
联系我们

#### 注：算法最小示例

如果你不想使用方便的 LagCompensator 组件，那么你可以在 Examples 文件夹中查看我们的"Lag Compensation"演示。 它超级简单，甚至没有联网，只显示算法而没有组件。

换句话说：如果你正在使用组件，请忽略这个例子！

<figure><img src="../../.gitbook/assets/2023-06-29 - lag compensation estimated time accurate by 6ms.png" alt=""> <figcaption><p>Mirror滞后补偿示例</p></figcaption></figure>

**这个演示很容易理解：**

- 填充的**白色正方形**是服务器上的对象
- 带轮廓的白色方块是每个时间间隔的**捕获**。
- **黑色方块**是客户端上的对象，**滞后**几毫秒
- 单击黑色方块，这将调用服务器上的`CmdFire()`。
- 如果是蓝色，你就击中了。 如果它突出了洋红色，你没有击中。
- **黑色方块**正好是**白色方块**后面的一个方块，这意味着你只有在滞后补偿功能正常的情况下才能击中。

这是滞后补偿第一部分：算法。

你可以在 games *today*中使用它，你只需要根据你的需要调整演示。

你还需要担心回滚物理世界，我们还没有这个演示。
