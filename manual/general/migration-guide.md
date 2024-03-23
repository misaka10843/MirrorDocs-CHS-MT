# 迁移指南

## 从 UNet 迁移项目(HLAPI)<a href="#migrating-a-project-from-unet-hlapi" id="migrating-a-project-from-unet-hlapi"></a>

本指南为您提供了将项目从 UNET 迁移到 Mirror 的分步说明。 Mirror 是 UNET 的一个分支。 因此，对于大多数项目来说，迁移是直接的。

您应该查看"[弃用"](deprecations.md)页面上的信息，以查看您的项目是否会受到影响。

还有一个[迁移工具，](https://github.com/Lymdun/MirrorConverter/)你可以试试。

### 1. 备份<a href="#1-backup" id="1-backup"></a>

你已经被警告过了。

### 2. 安装 Mirror 并重新启动 Unity<a href="#2-install-mirror-and-restart-unity" id="2-install-mirror-and-restart-unity"></a>

从[资源存储中](https://assetstore.unity.com/packages/tools/network/mirror-129321)获取 Mirror 并将其导入到项目中。

或者，如果你想冒险，你可以从 GitHub 上获取最新的[版本](https://github.com/vis2k/Mirror/releases)，但要知道，最前沿的开发版本不一定稳定。

**注意：**在将 Mirror 添加到项目后，您必须重新启动 Unity，以便组件菜单正确更新。

### 3. 替换命名空间<a href="#3-replace-namespace" id="3-replace-namespace"></a>

将`UnityEngine.Networking`替换为项目中的任何地方`Mirror`。 例如，如果你有这个：

```csharp
using UnityEngine.Networking;

public class Player : NetworkBehaviour
{
    ...
}
```

将其改为：

```csharp
using Mirror;

public class Player : NetworkBehaviour
{
    ...
}
```

在这一点上，你可能会得到一些编译错误。 不要惊慌，这些都很容易修复。 继续走...

### 4. 将 playerController 替换为 identity<a href="#4-replace-playercontroller-with-identity" id="4-replace-playercontroller-with-identity"></a>

将对`NetworkConnection.playerController`引用替换为`NetworkConnection.identity`.&# x20;

### 5. 删除网络设置<a href="#5-remove-networksettings" id="5-remove-networksettings"></a>

网络设置在 UNet 有渠道，但这是平面出打破。 而不是忽略您的设置，我们从 NetworkSettings 完全删除频道。 `sendInterval`现在也在代码和/或检查器中设置。

例如，如果你有这样的代码：

```csharp
[NetworkSettings(channel=1,sendInterval=0.05f)]
public class NetStreamer : NetworkBehaviour
{
    ...
}
```

将其改为：

```csharp
public class NetStreamer : NetworkBehaviour
{
    void Start()
    {
        syncInterval = 0.05f;
    }
}
```

请注意，默认传输[心灵感应](https://mirror-networking.com/docs/Articles/Transports/Telepathy.html)，完全忽略渠道，所有的消息都是可靠的，有序和碎片. 他们只是工作没有大惊小怪。 如果你想利用不可靠的通道，请尝试 UDP 或 Steam[传输](../transports/)。

### 6. 将 SyncListStruct 更改为 SyncList<a href="#6-change-syncliststruct-to-synclist" id="6-change-syncliststruct-to-synclist"></a>

在最初的 UNet Weaver 中有一个错误，它会在不检查名称空间的情况下扰乱我们`Mirror.SyncListStruct`。 在 Mirror 中，我们修复了 SyncLists，使其默认使用结构。

例如，如果你有这样的定义：

```csharp
public class SyncListQuest : SyncListStruct {}
```

改为：

```csharp
public class SyncListQuest : SyncList {}
```

### 7. 替换 NetworkHash128 和 NetworkInstanceId<a href="#7-replace-networkhash128-and-networkinstanceid" id="7-replace-networkhash128-and-networkinstanceid"></a>

它们已分别更改为 System.Guid 和 uint。

例如，如果你有这样的东西：

```csharp
public sealed class SpawnItemMessage : MessageBase
{
    public NetworkHash128 assetID;
    public NetworkInstanceId networkInstanceID;
    public Vector3 position;
    public Quaternion rotation;
}
```

替换为：

```csharp
public sealed struct SpawnItemMessage : NetworkMessage
{
    public System.Guid assetID;
    public uint networkInstanceID;
    public Vector3 position;
    public Quaternion rotation;
}
```

### 8. 更新 SyncList 回调<a href="#8-update-your-synclist-callbacks" id="8-update-your-synclist-callbacks"></a>

在 UNet 中，SyncLists 有一个回调委托，每当列表更新时，客户端都会调用它。 我们已经将回调改为 C#事件，并且我们还传递了更新/删除的项目。

例如，如果你有这样的代码：

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

将其改为：

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

请注意，回调也将在 Mirror 中的服务器中工作。

### 9. 更换组件<a href="#9-replace-components" id="9-replace-components"></a>

每个网络预制件和场景对象都需要调整。 他们将使用来自 Unet`NetworkIdentity`，您需要将该组件替换为来自 Mirror`NetworkIdentity`。 您可能正在使用其他网络组件，如`NetworkAnimator`或`NetworkTransform`。 Unet 中的所有组件都应替换为 Mirror 中相应的组件。

请注意，如果删除并添加 NetworkIdentity，则需要在引用它的任何组件中重新分配它。

### 10. 更新扩展组件<a href="#10-update-extended-components" id="10-update-extended-components"></a>

一些常用的扩展组件(如 NetworkManager)已更改了 Mirror 中的方法参数。 一个常用的覆盖是 OnServerAddPlayer。 使用原始的 HLAPI，您的覆盖可能看起来像这样：

```csharp
public override void OnServerAddPlayer(NetworkConnection conn, short playerControllerId, NetworkReader extraMessageReader)
{
    base.OnServerAddPlayer(conn, playerControllerId, extraMessageReader);
    // your code
}
```

在最新支持的 NetworkManager 中，如果您使用的是`OnServerAddPlayer`覆盖，请从覆盖和基本调用中删除"playerControllerId"和"extraMessageReader"参数：

```csharp
public override void OnServerAddPlayer(NetworkConnection conn)
{
    base.OnServerAddPlayer(conn);
    // your code
}
```

有关如何立即提交自定义角色的详细信息，请参阅自定义[玩家生成指南](../guides/gameobjects/custom-character-spawning.md)。

### 11. 选择您的交通工具<a href="#11-pick-your-transport" id="11-pick-your-transport"></a>

您可以在 Mirror 中选择几种传输方式之一。 打开你的 NetworkManager 游戏对象，在检查器中你会看到一个默认`TelepathyTransport`组件。 如果您希望使用基于 UDP 的传输，请拖动其中一个可用的传输并删除`TelepathyTransport`。

### 12. 配置地址和端口<a href="#12-configure-address-and-port" id="12-configure-address-and-port"></a>

在 HLAPI 中，您可以在 NetworkManager 中配置端口和本地地址。 我们的目标之一是使 Mirror 传输独立。 并非所有的传输都需要地址和端口。 有些传输甚至可能同时使用多个端口，因此这些设置是不够的。 我们从 NetworkManager 中删除了端口和地址以及所有其他网络信息属性，并将它们移至传输组件。

### 13. 更新防火墙和路由器<a href="#13-update-your-firewall-and-router" id="13-update-your-firewall-and-router"></a>

LLAPI 使用 UDP。 默认情况下，Mirror 使用 TCP。 这意味着您可能需要更改机器中的路由器端口转发和防火墙规则，以暴露 TCP 端口而不是 UDP。 这在很大程度上取决于您的路由器和操作系统。

## 视频版本<a href="#video-version" id="video-version"></a>

看看 uMMORPG 是如何迁移到 Mirror 的：

[http：//www.youtube.com/watch？v = LF9rTSS3rlI](http：//www.youtube.com/watch？v = LF9rTSS3rlI)

## 可能的错误消息<a href="#possible-error-messages" id="possible-error-messages"></a>

- TypeLoadException：发生类型加载异常。 - 如果您的项目中仍然有 SyncListStruct 而不是 SyncListStruct CT，则会发生这种情况。
- NullPointerException：最可能的原因是您替换了 NetworkIdentities 或其他组件，但您将它们分配到了某个位置。 重新分配这些引用。
- `error CS0246: The type or namespace name 'UnityWebRequest' could not be found. Are you missing 'UnityEngine.Networking' using directive?`

  Add this to the top of your script:

  ```
  using UnityWebRequest = UnityEngine.Networking.UnityWebRequest;
  ```

  `UnityWebRequest` is not part of UNet or Mirror, but it is in the same namespace as UNet. 将命名空间更改为 Mirror 导致脚本找不到 UnityWebRequest。 这同样适用于`WWW`和所有`UnityWebRequest`相关类。
