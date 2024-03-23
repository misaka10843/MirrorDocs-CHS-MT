# 迁移指南

## 从 UNet (HLAPI) 迁移项目 <a href="#migrating-a-project-from-unet-hlapi" id="migrating-a-project-from-unet-hlapi"></a>

本指南为您提供了逐步指导，以将您的项目从 UNET 迁移到 Mirror。Mirror 是 UNET 的一个分支。因此，对于大多数项目来说，迁移是直截了当的。

您应该查看[弃用](deprecations.md)页面上的信息，以查看您的项目是否会受到影响。

还有一个[迁移工具](https://github.com/Lymdun/MirrorConverter/)，您可以尝试使用。

### 1. 备份 <a href="#1-backup" id="1-backup"></a>

您已经被警告了。

### 2. 安装 Mirror 并重新启动 Unity <a href="#2-install-mirror-and-restart-unity" id="2-install-mirror-and-restart-unity"></a>

从[Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321)获取 Mirror 并将其导入到您的项目中。

或者，如果您感到冒险，您也可以从 GitHub 获取最新的[发布版本](https://github.com/vis2k/Mirror/releases)，但请注意，最新的开发版本未必稳定。

**注意：** 在将 Mirror 添加到项目后，您必须重新启动 Unity，以便正确更新组件菜单。

### 3. 替换命名空间 <a href="#3-replace-namespace" id="3-replace-namespace"></a>

在项目中的所有位置将 `UnityEngine.Networking` 替换为 `Mirror`。例如，如果您有以下内容：

```csharp
using UnityEngine.Networking;

public class Player : NetworkBehaviour
{
    ...
}
```

请将其替换为：

```csharp
using Mirror;

public class Player : NetworkBehaviour
{
    ...
}
```

此时，您可能会遇到一些编译错误。不要惊慌，这些很容易修复。继续前进...

### 4. 用 identity 替换 playerController <a href="#4-replace-playercontroller-with-identity" id="4-replace-playercontroller-with-identity"></a>

将对 `NetworkConnection.playerController` 的引用替换为 `NetworkConnection.identity`。&#x20;

### 5. 移除 NetworkSettings <a href="#5-remove-networksettings" id="5-remove-networksettings"></a>

UNet 中的 NetworkSettings 具有通道，但这是完全错误的。我们完全删除了 NetworkSettings 中的通道，而不是忽略您的设置。`sendInterval` 现在在代码中设置，也可以在检查器中设置。

请注意， 默认传输 [Telepathy](https://mirror-networking.com/docs/Articles/Transports/Telepathy.html) 完全忽略通道，所有消息都是可靠的、按顺序的和分段的。它们只是工作，没有麻烦。如果您想利用不可靠通道，请尝试使用 UDP 或 Steam 传输(../transports/)。

### 6. 将 SyncListStruct 更改为 SyncList <a href="#6-change-syncliststruct-to-synclist" id="6-change-syncliststruct-to-synclist"></a>

原始 UNet Weaver 中存在一个 bug，会在不检查命名空间的情况下干扰我们的 `Mirror.SyncListStruct`。在 Mirror 中，我们修复了 SyncLists，使其默认与结构体一起工作。

例如，如果您有以下定义：

```csharp
public class SyncListQuest : SyncListStruct {}
```

请将其替换为：

```csharp
public class SyncListQuest : SyncList {}
```

### 7. 替换 NetworkHash128 和 NetworkInstanceId <a href="#7-replace-networkhash128-and-networkinstanceid" id="7-replace-networkhash128-and-networkinstanceid"></a>

这些已更改为 System.Guid 和 uint。

例如，如果您有类似以下内容：

```csharp
public sealed class SpawnItemMessage : MessageBase
{
    public NetworkHash128 assetID;
    public NetworkInstanceId networkInstanceID;
    public Vector3 position;
    public Quaternion rotation;
}
```

请替换为：

```csharp
public sealed struct SpawnItemMessage : NetworkMessage
{
    public System.Guid assetID;
    public uint networkInstanceID;
    public Vector3 position;
    public Quaternion rotation;
}
```

### 8. 更新您的 SyncList 回调 <a href="#8-update-your-synclist-callbacks" id="8-update-your-synclist-callbacks"></a>

在 UNet 中，SyncLists 具有一个回调委托，每当列表更新时就会在客户端调用。我们已将回调更改为 C# 事件，并且还传递了被更新/移除的项。

例如，如果您有以下代码：

```csharp
using UnityEngine;
using UnityEngine.Networking;

public  class MyBehaviour : NetworkBehaviour
{
    public SyncListInt m_ints = new SyncListInt();

    private void OnIntChanged(SyncListInt.Operation op, int index)
    {
        Debug.Log("list changed " + op);
    }

    public override void OnStartClient()
    {
        m_ints.Callback = OnIntChanged;
    }
}
```

请将其替换为：

```csharp
using UnityEngine;
using Mirror;

public  class MyBehaviour : NetworkBehaviour
{
    public SyncListInt m_ints = new SyncListInt();

    private void OnIntChanged(SyncListInt.Operation op, int index, int oldItem, int newItem)
    {
        Debug.Log("list changed " + op + " old item: " + item + " new item: " + newItem);
    }

    public override void OnStartClient()
    {
        m_ints.Callback += OnIntChanged;
    }
}
```

请注意，回调在 Mirror 中也将在服务器端工作。

### 9. 替换组件 <a href="#9-replace-components" id="9-replace-components"></a>

每个网络预制和场景对象都需要进行调整。它们将使用 Unet 中的 `NetworkIdentity`，您需要将该组件替换为 Mirror 中的 `NetworkIdentity`。您可能正在使用其他网络组件，如 `NetworkAnimator` 或 `NetworkTransform`。所有来自 Unet 的组件都应替换为 Mirror 中对应的组件。

请注意，如果您移除并添加了 NetworkIdentity，您将需要在任何引用它的组件中重新分配它。

### 10. 更新扩展组件 <a href="#10-update-extended-components" id="10-update-extended-components"></a>

一些常见的扩展组件，比如 NetworkManager（网络管理器），在 Mirror 中已更改了方法参数。一个常用的重写是 OnServerAddPlayer（在服务器上添加玩家）。在原始的 HLAPI 中，您的重写可能如下所示：

```csharp
public override void OnServerAddPlayer(NetworkConnection conn, short playerControllerId, NetworkReader extraMessageReader)
{
    base.OnServerAddPlayer(conn, playerControllerId, extraMessageReader);
    // your code
}
```

在您新的支持 Mirror 的 NetworkManager 中，如果您正在使用 `OnServerAddPlayer` 重写，请从您的重写和基础调用中移除 "playerControllerId" 和 "extraMessageReader" 参数：

```csharp
public override void OnServerAddPlayer(NetworkConnection conn)
{
    base.OnServerAddPlayer(conn);
    // your code
}
```

查看自定义[玩家生成指南](../guides/gameobjects/custom-character-spawning.md)以了解如何提交自定义角色。

### 11. 选择您的传输方式 <a href="#11-pick-your-transport" id="11-pick-your-transport"></a>

您可以在 Mirror 中选择几种传输方式。打开您的 NetworkManager 游戏对象，在检视器中，您将默认看到一个 `TelepathyTransport`（心灵传输）组件。拖入其中一个可用的传输方式，并且如果您希望使用基于 UDP 的传输方式，则移除 `TelepathyTransport`。

### 12. 配置地址和端口 <a href="#12-configure-address-and-port" id="12-configure-address-and-port"></a>

在 HLAPI 中，您在 NetworkManager 中配置端口和本地地址。我们的目标之一是使 Mirror 独立于传输方式。并非所有传输方式都需要地址和端口。有些传输方式甚至可能同时使用多个端口，因此这些设置是不够的。我们从 NetworkManager 中移除了端口和地址以及所有其他网络信息属性，并将它们移到传输组件中。

### 13. 更新您的防火墙和路由器 <a href="#13-update-your-firewall-and-router" id="13-update-your-firewall-and-router"></a>

LLAPI 使用 UDP。Mirror 默认使用 TCP。这意味着您可能需要更改路由器端口转发和防火墙规则，以在您的机器上暴露 TCP 端口而不是 UDP。这高度取决于您的路由器和操作系统。

## 视频版本 <a href="#video-version" id="video-version"></a>

看看 uMMORPG 是如何迁移到 Mirror 的：

[http://www.youtube.com/watch?v=LF9rTSS3rlI](http://www.youtube.com/watch?v=LF9rTSS3rlI)

## 可能的错误消息 <a href="#possible-error-messages" id="possible-error-messages"></a>

* TypeLoadException: 发生了类型加载异常。- 如果您的项目中仍然使用 SyncListStruct 而不是 SyncListSTRUCT，就会发生这种情况。
* NullPointerException: 最可能的原因是您替换了 NetworkIdentities 或其他组件，但是您在某处已经将它们分配了。重新分配这些引用。
*   `error CS0246: 无法找到类型或命名空间名称 'UnityWebRequest'。您是否缺少 'UnityEngine.Networking' using 指令？`

    将以下内容添加到您的脚本顶部：

    ```
    using UnityWebRequest = UnityEngine.Networking.UnityWebRequest;
    ```

    `UnityWebRequest` 不是 UNet 或 Mirror 的一部分，但它与 UNet 在同一个命名空间中。将命名空间更改为 Mirror 导致您的脚本找不到 UnityWebRequest。对于 `WWW` 和所有与 `UnityWebRequest` 相关的类也是同样的情况。
