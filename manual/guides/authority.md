# 权威 (Authority)

## 网络权限 <a href="#network-authority" id="network-authority"></a>

权限是决定谁拥有对象并对其进行控制的一种方式。

### 服务器权限 <a href="#server-authority" id="server-authority"></a>

服务器权限意味着服务器控制一个对象。服务器默认拥有对象的权限。这意味着服务器将管理和控制可收集物品、移动平台、NPC 和任何不是玩家的其他网络对象。

### 客户端权限 <a href="#client-authority" id="client-authority"></a>

客户端权限意味着客户端控制一个对象。

当客户端对一个对象拥有权限时，这意味着他们可以调用[Commands](communications/remote-actions.md#commands)并且当客户端断开连接时，对象将自动销毁。

即使客户端对一个对象拥有权限，服务器仍然控制 SyncVar 和其他序列化功能。组件需要使用[Commands](communications/remote-actions.md#commands)来更新服务器上的状态，以便同步到其他客户端。

### 如何授予权限 <a href="#how-to-give-authority" id="how-to-give-authority"></a>

默认情况下，服务器对所有对象拥有权限。服务器可以授予客户端需要控制的对象权限，比如玩家对象。

如果使用 `NetworkServer.AddPlayerForConnection` 来生成玩家对象，则该对象将自动获得权限。

#### 使用 NetworkServer.Spawn <a href="#using-networkserverspawn" id="using-networkserverspawn"></a>

当对象生成时，您可以通过传递连接给生成消息来授予客户端权限。

```csharp
GameObject go = Instantiate(prefab);
NetworkServer.Spawn(go, connectionToClient);
```

#### 使用 identity.AssignClientAuthority <a href="#using-identityassignclientauthority" id="using-identityassignclientauthority"></a>

您可以随时通过使用 `AssignClientAuthority` 来授予客户端权限。这可以通过在要授予权限的对象上调用 `AssignClientAuthority` 来实现。

```csharp
identity.AssignClientAuthority(conn); // 当玩家拾取物品时，您可能需要执行此操作
```

### 如何移除权限 <a href="#how-to-remove-authority" id="how-to-remove-authority">(How to remove authority)</a>

您可以使用 `identity.RemoveClientAuthority` 来从对象中移除客户端权限。

```csharp
identity.RemoveClientAuthority();
```

无法从玩家对象中移除权限。相反，您需要使用 `NetworkServer.ReplacePlayerForConnection` 来替换玩家对象。

### 关于权限 <a href="#on-authority" id="on-authority"></a>

当给予对象权限或从对象中移除权限时，将向客户端发送消息以通知他们。这将导致调用 `OnStartAuthority` 或 `OnStopAuthority` 函数。

### 检查权限 <a href="#check-authority" id="check-authority" id="check-authority"></a>

#### 客户端端 <a href="#client-side" id="client-side"></a>

`identity.isOwned` 属性可用于检查本地玩家是否对对象具有权限。

#### 服务器端 <a href="#server-side" id="server-side"></a>

可以检查 `identity.connectionToClient` 属性，以查看哪个客户端对对象具有权限。如果为 null，则表示服务器具有权限。
