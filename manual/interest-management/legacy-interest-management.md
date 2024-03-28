---
description: 用于传统 Network Visibility（网络可见性）兴趣管理系统的文档。
---

# 传统（Legacy）

{% hint style="danger" %}
本页面上的组件是**过时的**，将在 Mirror 未来版本中移除。
{% endhint %}

多人游戏使用网络可见性的概念来确定在游戏过程中的任何时间点，哪些玩家可以看到哪些游戏对象。在一个具有移动视点和移动游戏对象的游戏中，通常玩家无法一次看到游戏中发生的所有事情。

如果在游戏过程中的某个特定时间点，某个玩家无法看到大多数其他玩家、非玩家角色或游戏中的其他移动或交互式物体，通常服务器无需向玩家客户端发送关于这些物体的信息。

这可以使您的游戏受益两方面：

- 减少玩家之间通过网络发送的数据量。这有助于提高游戏的响应性，减少带宽使用。您的多人游戏越大、越复杂，这个问题就越重要。
- 还有助于防止一些作弊行为。由于玩家客户端没有关于看不见的事物的信息，因此该玩家计算机上的黑客无法获取这些信息。

在网络环境中，“可见性”的概念并不一定与游戏对象是否直接在屏幕上可见有关。相反，它与是否应向特定客户端发送有关所讨论的游戏对象的数据有关。简单地说，如果客户端无法“看到”一个游戏对象，那么就不需要通过网络向该客户端发送有关该游戏对象的信息。理想情况下，您希望将通过网络发送的数据量限制在必要的范围内，因为通过网络发送大量不必要的数据可能会导致网络性能问题。

然而，准确确定游戏对象对于给定玩家是否真正可见可能也会耗费资源或复杂，因此通常最好使用更简单的计算来确定是否应向玩家发送关于其的网络数据 - 即是否“Network Visible”（网络可见）。在考虑这一点时，您要达到的平衡是在确定可见性的计算复杂性成本和通过网络发送比必要更多信息的成本之间。一个非常简单的计算方法是距离（接近）检查，Mirror 为此提供了一个内置组件。

## 网络接近检查器组件(Network Proximity Checker Component) <a href="#network-proximity-checker-component" id="network-proximity-checker-component"></a>

Mirror的[网络接近检查器(Network Proximity Checker)](../components/deprecated/network-proximity-checker.md)组件是实现玩家网络可见性的最简单方式。它与物理系统一起工作，以确定游戏对象是否足够接近（即在多人游戏中发送网络消息的“可见”目的）。

## 网络场景检查器组件(Network Scene Checker Component) <a href="#network-scene-checker-component" id="network-scene-checker-component"></a>

Mirror的[网络场景检查器(Network Scene Checker)](../components/deprecated/network-scene-checker.md)组件可用于在附加场景实例中隔离玩家和网络对象。

## 远程客户端上的网络可见性(Network Visibility on Remote Clients) <a href="#network-visibility-on-remote-clients" id="network-visibility-on-remote-clients"></a>

当远程客户端上的玩家加入网络游戏时，只有对玩家可见的游戏对象才会在该远程客户端上生成。这意味着即使玩家进入一个拥有许多网络游戏对象的大世界，游戏也可以快速启动，因为它不需要生成世界中存在的每个游戏对象。请注意，这适用于场景中的网络游戏对象，但不影响资源的加载。Unity仍然需要时间来加载注册的预制体和场景游戏对象的资源。

当玩家在世界中移动时，可见的网络游戏对象集合会发生变化。玩家的客户端会在发生这些变化时得到通知。当游戏对象不再可见时，客户端会收到`ObjectHide`消息。默认情况下，Mirror在接收到此消息时会销毁游戏对象。当游戏对象变得可见时，客户端会收到`ObjectSpawn`消息，就好像Mirror首次生成了游戏对象。默认情况下，游戏对象会像任何其他生成的游戏对象一样被实例化。

## 主机上的网络可见性(Network Visibility on the Host) <a href="#network-visibility-on-the-host" id="network-visibility-on-the-host"></a>


# 自定义网络可见性 (Customizing Network Visibility) <a href="#customizing-network-visibility" id="customizing-network-visibility"></a>

有时，您可能希望使用其他类型的可见性检查，例如基于网格的规则、视线测试、导航路径测试或适合您游戏的任何其他类型的测试。

为此，您可以通过单击“Create -> Mirror -> Network Observer”菜单中的[脚本模板](../general/script-templates.md)创建自己的自定义网络观察器。

了解网络接近检查器的工作原理可能会有所帮助。

网络接近检查器是使用 Mirror 的公共可见性接口实现的。使用相同的接口，您可以实现任何类型的可见性规则。每个`NetworkIdentity`都会跟踪其可见的玩家集合。一个`NetworkIdentity`游戏对象可见的玩家称为该`NetworkIdentity`的“观察者”。

网络接近检查器以固定间隔（在检视器中设置的“Vis Update Interval”值）调用`Network Identity`组件上的`RebuildObservers`方法，以便随着玩家移动，每个玩家的网络可见游戏对象集合得到更新。

### `NetworkVisibility`类（您的自定义观察者脚本继承自此类），其中包含一些用于确定可见性的虚拟函数。

* **OnCheckObserver（检查观察者）**（OnCheckObserver）\
  &#x20;此方法在服务器上调用，当新玩家进入游戏时，会对每个网络游戏对象进行调用。如果返回true，则将该玩家添加到对象的观察者列表中。网络接近检查器在此函数的实现中执行简单的距离检查，并使用`Physics.OverlapSphereNonAlloc`来查找在此对象的可见距离内的玩家。
* **OnRebuildObservers（重建观察者）**（OnRebuildObservers）\
  &#x20;此方法在调用`RebuildObservers`时在服务器上调用。此方法期望观察者集合中填充可以看到对象的玩家。然后，NetworkServer根据旧可见性集合和新可见性集合之间的差异发送`ObjectHide`和`ObjectSpawn`消息。
* **OnSetHostVisibility（设置主机可见性）**（OnSetHostVisibility）\
  &#x20;此方法由对象主机上的可见性系统调用。主机上的对象（具有本地客户端）在本地客户端看不到时不能被禁用或销毁。因此，调用此函数以允许自定义代码隐藏这些对象。典型的实现将在对象上禁用渲染器组件。仅在主机上的本地客户端上调用此方法。

您可以通过检查`NetworkIdentity`是否具有有效的`connectionToClient`来检查任何给定的网络游戏对象是否为玩家。例如：

```csharp
int hitCount = Physics.OverlapSphereNonAlloc(transform.position, visRange, hitsBuffer3D, castLayers);
for (int i = 0; i < hitCount; i++)
{
    Collider hit = hitsBuffer3D[i];
    NetworkIdentity identity = hit.GetComponent();

    if (identity != null && identity.connectionToClient != null)
        observers.Add(identity.connectionToClient);
}
```
