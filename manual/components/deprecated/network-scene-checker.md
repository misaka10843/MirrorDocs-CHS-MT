---
description: '已弃用: 使用场景兴趣管理替代。'
---

# 网络场景检查器

{% hint style="danger" %}
**网络场景检查器已过时 - 请改用** [**场景兴趣管理**](../../interest-management/scene.md) **。**
{% endhint %}

网络场景检查器组件控制网络客户端的游戏对象可见性，根据它们所在的场景。

![](<../../../.gitbook/assets/image (83).png>)

* **强制隐藏**\
  &#x20;选中此复选框可将此对象从所有玩家中隐藏。

使用网络场景检查器，运行在客户端上的游戏没有关于不可见游戏对象的信息。这有两个主要好处：减少通过网络发送的数据量，并使您的游戏更安全，防止黑客攻击。

此组件通常在服务器加载了几个子场景并需要将网络对象隔离到它们所在的子场景时使用。

具有网络场景检查器组件的游戏对象还必须具有网络标识组件。当您在游戏对象上创建网络场景检查器组件时，如果该游戏对象尚未具有网络标识组件，Mirror 也会在该游戏对象上创建网络标识组件。

具有网络场景检查器组件的场景对象在不在同一场景时会被禁用，并且生成的对象在不在同一场景时会被销毁。

## 与附加场景一起使用 <a href="#use-with-additive-scenes" id="use-with-additive-scenes"></a>

在 Mirror 中，服务器和连接的客户端始终位于相同的主场景上，但服务器和客户端可以具有各种组合的较小子场景以附加方式加载。服务器可能在启动时加载所有子场景，或者根据需要动态加载和卸载子场景，以满足玩家或其他活动的需求。

所有玩家对象始终首先生成在主场景中，该场景可能具有视觉内容、网络对象等。通过将此组件附加到所有网络对象，每当玩家对象从主场景或其他子场景移动到子场景时，新场景和先前场景中对象的观察者列表会相应更新。

在服务器上加载子场景是通过`SceneManager`的正常流程进行的：

```csharp
SceneManager.LoadSceneAsync(subScene, LoadSceneMode.Additive);
```

接下来，您将向客户端发送一个`SceneMessage`，告诉它以附加方式加载子场景：

```csharp
SceneMessage msg = new SceneMessage
{
    sceneName = subScene,
    sceneOperation = SceneOperation.LoadAdditive
};

connectionToClient.Send(msg);
```

然后，在仅服务器端，您只需将玩家对象移动到子场景中：

```csharp
// Position the player object in world space first
// This assumes it has a NetworkTransform component that will update clients
player.transform.position = new Vector3(100, 1, 100);

// Then move the player object to the subscene
SceneManager.MoveGameObjectToScene(player, subScene);
```

可选地，您可以向客户端发送另一个带有`SceneOperation.UnloadAdditive`的`SceneMessage`，以移除客户端不再需要的任何先前的附加场景。这适用于在关卡更改后有关卡的游戏。在移除之前可能需要短暂延迟，以允许客户端完全同步。

根据您的游戏复杂性，当在子场景之间切换玩家时，将玩家对象首先移动到主场景，等待100毫秒，重新定位它，最后将其移动到新的子场景可能会有所帮助。
