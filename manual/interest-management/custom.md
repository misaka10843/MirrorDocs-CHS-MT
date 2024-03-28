---
description: 自定义兴趣管理
---

# Custom(自定义)

## Custom Interest Management(自定义兴趣管理)

**Mirror** 允许您实现自定义兴趣管理解决方案。例如：

* 基于**射线投射**，这样 DotA 玩家就看不到墙后或灌木丛中的其他玩家
* **预测性射线投射**，适用于类似 Counter-Strike 的游戏。透视作弊可以显示墙后的玩家。您可以创建一个自定义兴趣管理系统，仅在玩家即将从墙后冒出时向玩家发送敌人。在第一人称射击游戏中，玩家移动速度很快，因此您仍然需要在他们变得可见前几毫秒发送他们。&#x20;

所有上述自定义解决方案在 Mirror 中都是可能的。要了解如何实现兴趣管理，请让我们逐步进行。

### 脚本模板

Mirror 包含一个用于自定义兴趣管理的[**脚本模板**](../general/script-templates.md)。它已经为您完全注释，并且所有虚拟方法重写已经为您存根。如果您之前使用过我们的传统兴趣管理系统，那么这些应该看起来很熟悉。

* **OnCheckObserver** 在某人生成时调用。如果 'identity' 可以被 'newObserver' 看到，则返回 true
* **OnRebuildObservers** 为给定的**网络标识**重建观察者。结果存储在**newObservers**中。&#x20;
  * Mirror 将自动将**newObservers**内部放入**identity.observers**。我们不直接执行此操作，因为这比添加/删除要复杂一些。Mirror 会处理它。无需担心 :)
* **RebuildAll** 是一个辅助函数，用于重建每个**生成的**网络标识的观察者。
  * 实现可能希望每隔一段时间调用此函数。

### **距离** 示例

距离兴趣管理是最简单、直接的实现方式。让我们逐步进行，看看如何从抽象的`InterestManagement`类继承。

```csharp
public class DistanceInterestManagement : InterestManagement
{
    [Tooltip("The maximum range that objects will be visible at.")]
    public int visRange = 10;

    [Tooltip("Rebuild all every 'rebuildInterval' seconds.")]
    public float rebuildInterval = 1;
    double lastRebuildTime;

    public override bool OnCheckObserver(NetworkIdentity identity, NetworkConnection newObserver)
    {
        return Vector3.Distance(identity.transform.position, newObserver.identity.transform.position) <= visRange;
    }

    public override void OnRebuildObservers(NetworkIdentity identity, HashSet<NetworkConnection> newObservers, bool initialize)
    {
        Vector3 position = identity.transform.position;
        
        // for each connection
        foreach (NetworkConnectionToClient conn in NetworkServer.connections.Values)
            // if authenticated and joined the world
            if (conn != null && conn.isAuthenticated && conn.identity != null)
                // check distance to our 'identity'
                if (Vector3.Distance(conn.identity.transform.position, position) < visRange)
                    // add to result
                    newObservers.Add(conn);
    }

    [ServerCallback]
    void Update()
    {
        // rebuild all spawned NetworkIdentity's observers every interval
        if (NetworkTime.time >= lastRebuildTime + rebuildInterval)
        {
            RebuildAll();
            lastRebuildTime = NetworkTime.time;
        }
    }
}
```

这并不太复杂，对吧？

* **OnCheckObserver** 简单地比较新生成标识与连接之间的距离。连接有一个主玩家，这是我们在这里用于距离检查的内容。
  * _请注意，您还可以针对连接拥有的每个对象进行检查。例如，如果 Bob 生成并且 Alice 不够接近，Alice 的宠物可能足够接近，因此 Alice 和 Bob 仍然应该看到彼此，尽管有点超出正常范围。_
* **OnRebuildObservers** 的工作是返回那些可以看到我们的**网络标识**的**网络连接**的 HashSet。因此，我们只需迭代所有**NetworkServer.connections**并检查它们的主玩家与我们的网络标识的距离。
  * _请注意，我们仅检查已经认证并在世界中拥有玩家的连接。仍在登录或选择角色的连接不应观察任何内容。_
* **Update** 的工作是定期调用**RebuildAll()**。如果我们不调用**RebuildAll()**，那么 Mirror 将永远不会重建观察者。&#x20;

### 主机模式可见性 (Host Mode Visibility)

在主机模式下，有人在运行服务器的同时也在服务器上玩游戏，因此你可能会认为：

* **我就是服务器**。**服务器可以看到所有人**。因此，**我应该看到所有人**。

这在技术上是 _正确的_，但如果你曾经有幸参加过**局域网**聚会，你会记得这种情况：

![最美好的时光。](<../../.gitbook/assets/image (42).png>)

&#x20;例如，有人在局域网上主持了一个 Counter-Strike 或 DotA 游戏。让我们思考一下这种情况：

* **主机**运行**服务器**。**服务器**在内存中保存了**整个世界**的状态，但**主机玩家**只能看到他周围的世界。

这个想法是让**主机玩家**成为游戏中的普通玩家。如果你在 DotA / Counter Strike 中玩游戏时，主机总是能看到其他人的位置，那局域网聚会就不会太有趣，对吧？

{% hint style="info" %}
**显然，主机可以作弊。**如果你在局域网上作弊，那你需要专业帮助。
{% endhint %}

Mirror 有一个虚拟方法 `SetHostVisibility(NetworkIdentity, bool)`，用于在主机模式下启用 / 禁用渲染器。换句话说，世界状态仍然存在 - 主机玩家只是看不到它。你可以在自定义系统中覆盖此方法以满足你的需求。
