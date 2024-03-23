---
description: 了解 Mirror 用于快节奏游戏的延迟补偿。
---

# 延迟补偿

延迟补偿（又称回滚）适用于快节奏/第一人称射击游戏。

对于大型多人在线角色扮演游戏、卡牌游戏、回合制游戏、策略游戏等，这并不重要。

## 为什么需要延迟补偿

让我们看看游戏需要延迟补偿的最常见原因。

考虑一个像《反恐精英》这样的第一人称射击游戏。

服务器上有两名玩家：你和你的朋友。

服务器将你朋友的位置与你同步。假设需要**50毫秒**。

你通过使用 Mirror 的 `[Command] CmdFire(Vector3 direction)` 开火射击你的朋友。

`[Command]` 再花费**50毫秒**到达服务器。

服务器然后检查你是否击中了你的朋友，并造成伤害。

{% hint style="danger" %}
在经过**100毫秒**的时间后，你的朋友已经向左移动了两步。

因此，你将始终**射偏**。
{% endhint %}

<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption><p>你看到的朋友位置与实际位置。图片来源：<a href="https://developer.valvesoftware.com/wiki/Source_Multiplayer_Networking">Valve's Article</a>。</p></figcaption></figure>

这就是为什么快节奏/第一人称射击游戏需要延迟补偿。

否则玩家将始终需要瞄准目标的前方。

{% hint style="info" %}
从理论上看，这似乎并不是什么大问题。\
但在实践中，玩家会立即抱怨他们的瞄准不准。

延迟补偿开启/关闭的区别对每个人都非常明显。
{% endhint %}

要更深入地了解，请阅读[Valve 的 Source Multiplayer Networking 文章](https://developer.valvesoftware.com/wiki/Source\_Multiplayer\_Networking)。这是一篇很好的文章，即使你现在还不完全理解，也值得花时间阅读。

## Mirror 中的延迟补偿

我们的延迟补偿分为两部分。

#### LagCompensation.cs 独立算法

首先，有一个独立的、与 Unity 无关的、具有完整测试覆盖的 C# LagCompensation.cs 算法：

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-26-07@2x.png" alt="" width="344"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/2023-07-05 - 14-28-43@2x.png" alt=""><figcaption></figcaption></figure>

该算法可以记录和采样任何类型为\<T>的历史记录。\
换句话说，您可以根据需要自定义适配它。\
请记住，这是非常底层的操作，更容易使用高级组件！

#### 拖尾补偿器组件 (Lag Compensator Component)

第二部分：我们的 `Lag Compensator` 组件：

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

这个组件将所有魔法隐藏在一个组件中，自动记录3D碰撞器快照的历史记录。只需将其添加到您的玩家对象中，它将为您处理一切！

接下来，找到您的玩家在本地开火的位置。应该有一个类似 CmdFireWeapon() 的 \[Command]，将输入发送到服务器。在这里，我们不是检查玩家是否击中另一个玩家，而是首先询问拖尾补偿器在玩家开火时的其他玩家位置。

您可以查看我们的射击示例，看看它是如何运作的，就像这样简单：

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

请注意，上面的代码略有简化，但除了在LagCompensator的'Check'函数中还有一些更多可选配置参数之外，这就是全部内容。

{% hint style="info" %}
我们的拖尾补偿器非常易于使用，如果需要，您仍然可以添加更复杂的检查。重要的是，我们独立的LagCompensation.cs算法是通用\<T>的，可以与您需要的任何内容（2D、3D、自定义物理等）一起使用。
{% endhint %}

{% hint style="success" %}
我们的Mirror射击示例使用了拖尾补偿器。\
截至2024年2月，它几乎完成，即将发布！
{% endhint %}

#### 注意：算法最小示例

如果您不想使用方便的LagCompensator组件，那么您可以查看我们在示例文件夹中的 'Lag Compensation' 演示。这是非常简单的，甚至没有网络连接，只显示算法而没有组件。

换句话说：如果您正在使用该组件，请忽略此示例！

<figure><img src="../../.gitbook/assets/2023-06-29 - lag compensation estimated time accurate by 6ms.png" alt=""><figcaption><p>Mirror的延迟补偿示例</p></figcaption></figure>

**这个演示非常容易理解：**

* 填充的**白色方块**是服务器上的一个对象
* 轮廓的白色方块是每个间隔拍摄的**捕获**
* **黑色方块**是客户端上的对象，**滞后**几毫秒
* 您点击黑色方块，这会在服务器上调用`CmdFire()`
* 如果它高亮显示为蓝色，表示击中。如果高亮显示为品红色，表示未击中。
* **黑色方块**恰好比**白色方块**滞后一个方块，这意味着只有在延迟补偿功能正常工作时才会击中。

这是延迟补偿的第一部分：算法。

您可以在游戏中**今天**使用这个，只需将演示调整到您的需求。

您还需要担心回滚物理世界，我们目前还没有这方面的演示。
