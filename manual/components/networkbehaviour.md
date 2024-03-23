# 网络行为

{% hint style="info" %}
请参阅 [NetworkBehaviour](https://storage.googleapis.com/mirror-api-docs/html/db/d21/class\_mirror\_1\_1\_network\_behaviour.html) 在 API 参考中的页面。
{% endhint %}

网络行为脚本与具有网络身份组件的游戏对象一起工作。这些脚本可以执行高级 API 功能，如 Commands（命令）、ClientRpc's（客户端远程过程调用）和 SyncVars（同步变量）。

{% hint style="danger" %}
不要在 `Awake` 中将对象放入 `DontDestroyOnLoad`（DDOL）。\
您可以在 `Start` 中执行此操作。
{% endhint %}

使用 Mirror 的服务器验证系统，服务器必须使用 `NetworkServer.Spawn` 函数生成具有网络身份组件的游戏对象。通过这种方式生成它们会为它们分配一个 `netId` 并在连接到服务器的客户端上创建它们。

**注意：** 这不是您可以直接添加到游戏对象的组件。相反，您必须创建一个从 `NetworkBehaviour`（而不是默认的 `MonoBehaviour`）继承的脚本，然后将您的脚本作为组件添加到游戏对象中。

## 属性 <a href="#properties" id="properties"></a>

* **isServer**（服务器上）\
  如果此游戏对象已生成，则在服务器上返回 true。
* **isClient**（客户端上）\
  如果此游戏对象由服务器生成，则在客户端上返回 true。
* **isLocalPlayer**（本地玩家）\
  如果此游戏对象代表为此客户端创建的玩家，则在客户端上返回 true。
* **isOwned**（以前为 `hasAuthority`）\
  如果此客户端对此游戏对象具有[权限](../guides/authority.md)，则在客户端上返回 true。在服务器上的上下文中无意义。
* **netId**（网络 ID）\
  此游戏对象的唯一网络 ID。服务器在运行时分配此 ID。对于网络上的所有游戏对象，此 ID 都是唯一的。
* **netIdentity**（网络身份）\
  返回此对象的网络身份
* **connectionToServer**（连接到服务器）\
  与附加到此游戏对象的网络身份组件相关联的网络连接。这仅对客户端上的**本地玩家对象**有效，并且对于客户端上可能存在的其他玩家对象为 null。
* **connectionToClient**（连接到客户端）\
  与附加到此游戏对象的网络身份组件相关联的网络连接。对于已分配给特定客户端的服务器上的游戏对象有效，例如玩家对象、宠物、助手或单个客户端“拥有”的其他对象。

网络行为脚本具有以下功能：

* 同步变量
* 网络回调
* 服务器和客户端函数
* 发送命令
* 客户端 RPC 调用
* 网络事件

![](<../../.gitbook/assets/image (96).png>)

## 网络回调 <a href="#network-callbacks" id="network-callbacks"></a>

有内置的回调函数会在不同的网络事件上调用网络行为脚本。这些是基类上的虚函数，因此您可以像这样在自己的代码中重写它们：

```csharp
public class SpaceShip : NetworkBehaviour
{
    public override void OnStartServer()
    {
        // disable client stuff
    }

    public override void OnStartClient()
    {
        // register client events, enable effects
    }
}
```

内置的回调包括：

* **OnStartServer** 在服务器上调用，当游戏对象在服务器上生成时，或者当服务器为场景中的游戏对象启动时
* **OnStopServer** 在服务器上调用，当游戏对象在服务器上被销毁时，或者当服务器为场景中的游戏对象停止时
* **OnStartClient** 在客户端上调用，当游戏对象在客户端生成时，或者当客户端连接到服务器以为场景中的游戏对象启动时
* **OnStopClient** 在客户端上调用，当服务器销毁游戏对象时
* **OnStartLocalPlayer** 在客户端上调用，在本地客户端的玩家游戏对象的`OnStartClient`之后
* **OnStopLocalPlayer** 在客户端上调用，在本地客户端的玩家游戏对象的`OnStopClient`之前
* **OnStartAuthority** 在服务器分配权限后在所有者客户端上调用。在客户端环境中，`isOwned` 对于这样的对象将为 true。
* **OnStopAuthority** 在服务器移除权限后在所有者客户端上调用。

请注意，在点对点托管设置中，当一个客户端同时充当主机和客户端时，`OnStartServer` 和 `OnStartClient` 都会在同一游戏对象上调用。这两个函数对于特定于客户端或服务器的操作非常有用，例如在服务器上抑制效果，或者设置客户端事件。

## 服务器和客户端属性 <a href="#server-and-client-functions" id="server-and-client-functions"></a>

您可以使用自定义属性标记 Monobehaviour 和 Network Behaviour 脚本中的方法，将其指定为仅服务器或仅客户端函数。`[Server]` 和 `[ServerCallback]` 如果客户端未激活则立即返回。同样，`[Client]` 和 `[ClientCallback]` 如果服务器未激活则立即返回。

`[Server]`和`[Client]`属性不会在编译时生成错误，但如果在错误的上下文中调用，则会发出警告日志消息。

`ServerCallback`和`ClientCallback`属性的行为与上述相同，但不会导致生成警告。

有关更多信息，请参阅[Attributes](../guides/attributes.md)。

## Commands <a href="#commands" id="commands"></a>

要在服务器上执行代码，必须使用Commands。高级API是一个服务器授权系统，因此Commands是客户端触发服务器上某些代码的唯一方式。

通常只有玩家对象可以有Commands，但它们也可以存在于其他网络对象上，并且可以由为该对象分配了权限的客户端调用，或者由任何已明确指示了[绕过权限](../guides/communications/remote-actions.md)的客户端调用。

当客户端玩家游戏对象发送一个command时，该command会在服务器上对应的玩家游戏对象上运行。这种路由是自动发生的，因此不可能让客户端为不同的玩家发送command。

要在代码中定义一个Command，必须使用\[Command]属性装饰一个方法。虽然不是必需的，但强烈建议在方法名称前加上`Cmd`，这样在调用代码中更容易识别将要发生的事情。

Commands只需在客户端上正常调用方法即可。方法不会在客户端上运行，而是自动在服务器上对应的玩家游戏对象上调用。

Commands是类型安全的，具有内置的安全性和路由到玩家的机制，并使用高效的序列化机制来使调用它们更快。

有关更多信息，请参阅[Communications](../guides/communications/)和相关部分。

## Client RPC Calls <a href="#client-rpc-calls" id="client-rpc-calls"></a>

Client RPC调用是服务器游戏对象在客户端游戏对象上发生事情的一种方式。

Client RPC调用不仅限于玩家游戏对象，还可以在具有Network Identity组件的任何游戏对象上调用。

要在您的代码中定义客户端RPC调用，必须使用`[ClientRpc]`修饰该方法。虽然不是必需的，但强烈建议以`Rpc`前缀命名方法，这样在调用代码中更容易识别将要发生的事情。

请参阅[通信](../guides/communications/)和相关章节以获取更多信息。

## 网络事件 (已过时) <a href="#networked-events-obsolete" id="networked-events-obsolete"></a>

> **重要** SyncEvents 在版本 18.0.0 中已移除，更多信息请查看此[Issue](https://github.com/vis2k/Mirror/pull/2178)

网络事件类似于客户端RPC调用，但不是调用游戏对象上的函数，而是触发事件。

这使您可以编写脚本，当事件被触发时可以注册回调。

要在您的代码中定义网络事件，必须编写一个同时满足以下条件的函数：

* 以`Event`开头命名
* 具有`SyncEvent`属性

您可以使用事件构建强大的网络游戏系统，可以由其他脚本扩展。此示例展示了客户端上的效果脚本如何响应服务器上的战斗脚本生成的事件。

SyncEvent 是 Commands 和 ClientRpc 调用派生的基类。您可以在自己的函数上使用 SyncEvent 属性，以使您自己的基于事件驱动的网络游戏代码。使用 SyncEvent，您可以扩展 Mirror 的多人游戏功能，以更好地适应自己的编程模式。

请参阅[SyncEvents](../guides/synchronization/syncevent.md)以获取更多详细信息。
