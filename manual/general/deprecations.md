# 废弃功能

Unity Networking (UNet) 的某些功能已从 Mirror 中移除或修改，原因各不相同。本页面将识别所有已更改和移除的功能、属性和方法，变更或移除的原因，以及可能的替代方案。

{% hint style="info" %}
**注意：** 本文档中的某些更改可能适用于即将发布到 Asset Store 的版本。
{% endhint %}

## 匹配命名空间和主机迁移 <a href="#match-namespace--host-migration" id="match-namespace--host-migration"></a>

作为 Unity 服务的一部分，整个命名空间已被移除。一开始就不太好用，并且作为核心网络包的一部分非常复杂。我们预计，这个命名空间以及其他后端服务将通过独立应用程序提供，这些应用程序与 Mirror 集成。

## 简化网络服务器 <a href="#network-server-simple" id="network-server-simple"></a>

这个功能过于复杂且难以维护，所以被移除。有更简单的方法来创建基本的监听服务器，无论是否使用我们的传输方式。

## 本地合作 <a href="#couch-co-op" id="couch-co-op"></a>

通过移除这个低 hanging fruit，核心网络功能得到了极大简化。这个功能存在 bug，并且过于复杂，不值得修复。对于那些需要类似功能的人，考虑将一个不可见的玩家预制体定义为消息导管，用于生成具有客户端权限的实际玩家预制体。所有输入将通过导管预制体路由到控制玩家对象。

## 消息类型 <a href="#message-types" id="message-types"></a>

`MsgType` 枚举已被移除。所有消息类型都是动态生成的。请使用 `Send` 代替。

## 网络变换 <a href="#network-transform" id="network-transform"></a>

[网络变换](../components/network-transform/) 已完全替换，现在只同步位置、旋转和缩放，具有这些控制选项以及是否插值它们，并使用快照插值。它还有一堆虚拟方法，还有一个用于创建自己的派生版本的[脚本模板](script-templates.md)。刚体支持已被移除，取而代之的是单独的[网络刚体](../components/network-rigidbody.md) 组件。

## 网络动画器(Network Animator) <a href="#network-animator" id="network-animator"></a>

[网络动画器(Network Animator)](../components/network-animator.md)也进行了简化，因为它将所有动画器参数批量处理为单个更新消息。

## SyncVar 钩子参数(SyncVar Hook Parameters) <a href="#syncvar-hook-parameters" id="syncvar-hook-parameters"></a>

[SyncVar](../guides/synchronization/syncvars.md) 属性值现在在调用钩子之前更新，并且钩子现在需要两个与属性相同类型的参数：`oldValue` 和 `newValue`。

## SyncListSTRUCT <a href="#syncliststruct" id="syncliststruct"></a>

请使用[SyncList](../guides/synchronization/synclists.md)。

## SyncList 类

- `SyncListString` 被替换为 `SyncList<string>`。
- `SyncListFloat` 被替换为 `SyncList<float>`。
- `SyncListInt` 被替换为 `SyncList<int>`。
- `SyncListUInt` 被替换为 `SyncList<uint>`。
- `SyncListBool` 被替换为 `SyncList<bool>`。

查看[文档](../guides/synchronization/synclists.md)以获取更多详细信息。

## SyncList 操作 <a href="#synclist-operations" id="synclist-operations"></a>

- `OP_REMOVE` 被替换为 `OP_REMOVEAT`。
- `OP_DIRTY` 被替换为 `OP_SET`。

查看[文档](../guides/synchronization/synclists.md)以获取更多详细信息。

## SyncDictionary 操作 <a href="#syncidictionary-operations" id="syncidictionary-operations"></a>

- `OP_DIRTY` 被替换为 `OP_SET`。

查看[文档](../guides/synchronization/syncdictionary.md)以获取更多详细信息。

## SyncObject

- 现在是一个类而不是一个接口。
- `Flush` - 使用 `ClearChanges` 代替。

## 服务质量标志(Quality of Service Flags) <a href="#quality-of-service-flags" id="quality-of-service-flags"></a>

在经典 UNet 中，QoS 标志用于确定数据包如何到达远程端。例如，如果您需要一个数据包在队列中优先处理，您可以指定一个高优先级标志，然后 Unity LLAPI 将接收并适当处理。不幸的是，这给传输层带来了很多额外工作，并且一些 QoS 标志由于依赖过多魔法的错误代码而无法按预期工作。

在 Mirror 中，QoS 标志被替换为了一个“Channels”系统。虽然默认传输方式 [Telepathy](../transports/telepathy-transport.md) 不使用任何通道，因为它基于 TCP，但其他传输方式，比如 [Ignorance](../transports/ignorance.md) 和 [LiteNetLib](../transports/litenetlib-transport.md)，支持通道。

当前定义的通道包括：

- `Channels.Reliable = 0`（可靠通道）
- `Channels.Unreliable = 1`（不可靠通道）

## 按类别的更改 <a href="#changes-by-class" id="changes-by-class"></a>

### NetworkManager <a href="#networkmanager" id="networkmanager"></a>

- 在许多地方，`NetworkConnection` 被替换为了 `NetworkConnectionToClient`。
- `networkPort`\
  已移除，作为将传输方式分离为组件的一部分。并非所有传输方式都使用端口，但那些使用端口的传输方式有相应的字段。查看 [Transports](../transports/) 获取更多信息。
- `IsHeadless()`\
  使用编译符号 `UNITY_SERVER` 代替。
- `ConfigureServerFrameRate` 被重命名为 `ConfigureHeadlessFrameRate`。
- `client`\
  直接使用 NetworkClient，它很快将被设为静态。例如，使用 `NetworkClient.Send(message)` 而不是 `NetworkManager.client.Send(message)`。
- `IsClientConnected()`\
  使用静态属性 `NetworkClient.isConnected` 代替。
- `onlineScene` 和 `offlineScene`\
  &#x20;现在存储完整路径，因此请使用 SceneManager.GetActiveScene().path。
- `OnStartClient(NetworkClient client)`\
  &#x20;改为重写 OnStartClient()，因为所有 `NetworkClient` 方法现在都是静态的。
- `OnClientChangeScene(string newSceneName)`\
  &#x20;改为重写 `OnClientChangeScene(string newSceneName, SceneOperation sceneOperation, bool customHandling)`。
- `OnClientChangeScene(string newSceneName, SceneOperation sceneOperation)`\
  &#x20;改为重写 `OnClientChangeScene(string newSceneName, SceneOperation sceneOperation, bool customHandling)`。
- `OnServerAddPlayer(NetworkConnection conn, AddPlayerMessage extraMessage)`\
  &#x20;改为重写 `OnServerAddPlayer(NetworkConnection conn)`。查看 [自定义玩家生成指南](../guides/gameobjects/custom-character-spawning.md) 获取详细信息。
- `OnServerRemovePlayer(NetworkConnection conn, NetworkIdentity player)`\
  &#x20;使用 `NetworkServer.RemovePlayerForConnection(NetworkConnection conn, GameObject player, bool keepAuthority = false)` 代替。
- `OnServerError(NetworkConnection conn, int errorCode)`

* 用 `OnServerError(NetworkConnection conn, Exception exception)` 替换。

- 用 `OnClientError(Exception exception)` 替换。
- 移除了 `disconnectInactiveConnections` 和 `disconnectInactiveTimeout`。
- 在 `OnClient\*` 虚方法中不再需要 `NetworkConnection` 参数。从你的重写中移除该参数，而是在代码中使用 `NetworkClient.connection`。
- 将 `serverTickRate` 重命名为 `sendRate`。
- `serverTickInterval` 移至 `NetworkServer`。

### NetworkManagerHUD

- 移除了 `showGUI`。

  禁用组件即可。

### NetworkRoomManager <a href="#networkroommanager" id="networkroommanager"></a>

- 在许多地方，`NetworkConnection` 被替换为 `NetworkConnectionToClient`。
- `OnRoomServerCreateGamePlayer(NetworkConnection conn)`\
  使用 `OnRoomServerCreateGamePlayer(NetworkConnection conn, GameObject roomPlayer)` 替代。
- `OnRoomServerSceneLoadedForPlayer(GameObject roomPlayer, GameObject gamePlayer)`\
  使用 `OnRoomServerSceneLoadedForPlayer(NetworkConnection conn, GameObject roomPlayer, GameObject gamePlayer)` 替代。
- 客户端虚方法不再需要 `NetworkConnection` 参数。

  在你的重写中使用 `NetworkClient.connection`。

### NetworkIdentity <a href="#networkidentity" id="networkidentity"></a>

- `clientAuthorityOwner`\
  使用 `connectionToClient` 代替
- `GetSceneIdenity`\
  使用 `GetSceneIdentity` 代替（原名称中有拼写错误）
- `RemoveClientAuthority(NetworkConnection conn)`\
  不再需要 `NetworkConnection` 参数，也不返回任何内容
- `spawned` 字典

  已拆分为 `NetworkServer.spawned` 和 `NetworkClient.spawned` 字典。

- 本地玩家权限复选框\
  不再需要此复选框，我们简化了 Mirror 中 [Authority](../guides/authority.md) 的工作方式。

### NetworkBehaviour <a href="#networkbehaviour" id="networkbehaviour"></a>

- 在许多地方，`NetworkConnection` 被替换为 `NetworkConnectionToClient`。
- `sendInterval` 属性\
  使用 `NetworkBehaviour.syncInterval` 字段代替。也可以在检查器中进行修改。
- `List m_SyncObjects`\
  使用 `List syncObjects` 代替。
- `OnSetLocalVisibility(bool visible)`\
  改为重写 `OnSetHostVisibility(bool visible)`。
- `OnRebuildObservers`、`OnCheckObserver` 和 `OnSetHostVisibility` 移至名为 `NetworkVisibility` 的单独类。
- `NetworkBehaviour.OnNetworkDestroy` 重命名为 `NetworkBehaviour.OnStopClient`。
- `getSyncVarHookGuard` 重命名为 `GetSyncVarHookGuard`。
- `setSyncVarHookGuard` - 重命名为 `SetSyncVarHookGuard`。
- `SetDirtyBit` - 使用 `SetSyncVarDirtyBit` 代替。
- `[Command]` 属性参数 `ignoreAuthority` 替换为 `requiresAuthority`。
- `[ClientRpc]` 属性参数 `includeOwner` 替换为 `excludeOwner`。
- `hasAuthority` 重命名为 `isOwned`。

### NetworkConnection <a href="#networkconnection" id="networkconnection"></a>

- `hostId`\
  &#x20;因为自从我们移除了 LLAPI 作为默认选项后就不再需要了。对于常规连接，它始终为 0，对于本地连接则为 -1。使用 `connection.GetType() == typeof(NetworkConnection)` 来检查是常规连接还是本地连接。
- `isConnected`\
  &#x20;因为毫无意义而被移除。`NetworkConnection` 总是连接的。
- `InvokeHandlerNoData(int msgType)`\
  &#x20;改用 `InvokeHandler`。
- `playerController`\
  &#x20;重命名为 `identity`，因为它就是：连接的 `NetworkIdentity`。
- `RegisterHandler(short msgType, NetworkMessageDelegate handler)`\
  &#x20;改用 `NetworkServer.RegisterHandler()` 或 `NetworkClient.RegisterHandler()`。
- `UnregisterHandler(short msgType)`\
  &#x20;改用 `NetworkServer.UnregisterHandler()` 或 `NetworkClient.UnregisterHandler()`。
- `Send(int msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;改用 `Send(msg, channelId)`。

### NetworkServer <a href="#networkserver" id="networkserver"></a>

- `NetworkConnection` 在许多地方被替换为 `NetworkConnectionToClient`。
- `FindLocalObject(uint netId)`\
  &#x20;改用 `NetworkServer.spawned[netId].gameObject`。
- `RegisterHandler(int msgType, NetworkMessageDelegate handler)`\
  &#x20;改用 `RegisterHandler(T msg)`。
- `RegisterHandler(MsgType msgType, NetworkMessageDelegate handler)`\
  &#x20;改用 `RegisterHandler(T msg)`。
- `RegisterHandler(Action handler, bool requireAuthentication = true)`

  改用 `RegisterHandler(Action<NetworkConnection, T), requireAuthentication = true)`。

- `UnregisterHandler(int msgType)`\
  &#x20;改用 `UnregisterHandler(T msg)`。
- `UnregisterHandler(MsgType msgType)`\
  &#x20;改用 `UnregisterHandler(T msg)`。
- `SendToAll(int msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;改用 `SendToAll(T msg, int channelId = Channels.Reliable)`。
- `SendToClient(int connectionId, int msgType, MessageBase msg)`\
  改用 `NetworkConnection.Send(T msg, int channelId = Channels.Reliable)`。
- `SendToClient(int connectionId, T msg)`\
  改用 `NetworkConnection.Send(T msg, int channelId = Channels.Reliable)`。
- `SendToClientOfPlayer(NetworkIdentity identity, int msgType, MessageBase msg)`\
  改用 `identity.connectionToClient.Send<T>(T message, int channelId = Channels.Reliable)`。
- `SendToReady(NetworkIdentity identity, short msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;改用 `identity.connectionToClient.Send()`。
- `SendToReady(NetworkIdentity identity, T message, bool includeOwner = true, int channelId = Channels.Reliable)`

* 已更名为 `SendToReadyObservers`。

- `SpawnWithClientAuthority(GameObject obj, GameObject player)`\
  &#x20;改用 `Spawn(GameObject obj, GameObject player)`。
- `SpawnWithClientAuthority(GameObject obj, NetworkConnection ownerConnection)`\
  &#x20;改用 `Spawn(GameObject obj, NetworkConnection ownerConnection)`。
- `SpawnWithClientAuthority(GameObject obj, Guid assetId, NetworkConnection ownerConnection)`\
  &#x20;改用 `Spawn(GameObject obj, Guid assetId, NetworkConnection ownerConnection)`。
- `disconnectInactiveConnections` 和 `disconnectInactiveTimeout` 已移除。
- `NoConnections` 已更名为 `NoExternalConnections`。
- `DisconnectAllExternalConnections` / `DisconnectAllConnections`

  改用 `DisconnectAll`。

- `OnError` 更名为 `OnTransportError` 以增强清晰度。

### NetworkClient <a href="#networkclient" id="networkclient"></a>

- `NetworkClient singleton`\
  &#x20;直接使用 `NetworkClient`。不再需要 Singleton，因为所有函数现在都是静态的。\
  &#x20;示例：使用 `NetworkClient.Send(message)` 而不是 `NetworkClient.singleton.Send(message)`。
- `allClients`\
  &#x20;直接使用 `NetworkClient`。始终只有一个客户端。
- `GetRTT()`\
  &#x20;改用 `NetworkTime.rtt`。
- `readyConnection`

  改用 `connection`。

- `isLocalClient`

  改用 `isHostClient`。

- `DisconnectLocalServer()`

  改用 `NetworkClient.Disconnect()`。

- `RegisterHandler(int msgType, NetworkMessageDelegate handler)`\
  &#x20;改用 `RegisterHandler(T msg)`。
- `RegisterHandler(MsgType msgType, NetworkMessageDelegate handler)`\
  &#x20;改用 `RegisterHandler(T msg)`。
- `RegisterHandler(Action<NetworkConnection, T> handler, bool requireAuthentication = true)`

  改用 `RegisterHandler(Action<T> handler, bool requireAuthentication = true)`。

- `UnregisterHandler(int msgType)`\
  &#x20;改用 `UnregisterHandler(T msg)`。
- `UnregisterHandler(MsgType msgType)`\
  &#x20;改用 `UnregisterHandler(T msg)`。
- `Ready(NetworkConnection conn)`

使用 `Ready()` 而不带 `NetworkConnection` 参数。

- `Send(short msgType, MessageBase msg)`\
  使用 `Send(T msg, int channelId = Channels.Reliable)` 而不带消息 id。

- `ShutdownAll()`\
  使用 `Shutdown()`。只有一个客户端。

- `OnError` 更名为 `OnTransportError` 以增加清晰度。

### ClientScene <a href="#clientscene" id="clientscene"></a>

- 已合并到 `NetworkClient`。

### Network Scene Checker

- 已被 [场景兴趣管理](../interest-management/scene.md) 取代。

### Network Proximity Checker

- 已被 [空间哈希](../interest-management/spatial-hashing.md) / [距离](../interest-management/distance.md) 兴趣管理取代。

### Network Match Checker

- 已被 Network Match 取代，并需要 [匹配兴趣管理](../interest-management/match.md)。

### Network Owner Checker

- 已被 Network Team 取代，并需要 [团队兴趣管理](../interest-management/team.md)。

### Network Authenticator

- `NetworkConnection` 在许多地方被 `NetworkConnectionToClient` 取代。
- `OnClientAuthenticate` 不再接受 `NetworkConnection` 参数。\
  需要时请使用 `NetworkClient.connection`。
- `OnClientAuthenticated` 事件不再接受 `NetworkConnection` 参数。\
  需要时请使用 `NetworkClient.connection`。
- 在客户端消息处理程序中不再使用 `NetworkConnection`。

  请在处理程序中使用 `NetworkClient.connection`。

- `ClientAccept` 和 `ClientReject` 不再需要 `NetworkConnection` 参数。

### NetworkTime

- `NetworkTime.timeVar` 更名为 `timeVariance`。
- `NetworkTime.timeSd` 更名为 `timeStandardDeviation`。
- `NetworkTime.rttVar` 更名为 `rttVariance`。
- `NetworkTime.rttSd` 更名为 `rttStandardDeviation`。

### Transport

- `activeTransport` 更名为 `active`。

### Messages <a href="#messages" id="messages"></a>

基本的简单类型消息都被移除，因为它们是多余的。您可以自己创建消息类。

- `StringMessage`
- `ByteMessage`
- `BytesMessage`
- `IntegerMessage`
- `DoubleMessage`
- `EmptyMessage`

### NetworkMessage 需要在所有情况下使用结构体 - 不再支持类

### NetworkReader <a href="#networkreader" id="networkreader"></a>

- `Read(byte[] buffer, int offset, int count)`\
  &#x20;使用 `ReadBytes` 代替。
- `ReadPackedInt32(int value)` 使用 `ReadInt32(int value)` 代替。
- `ReadPackedUInt32(uint value)` 使用 `ReadUInt32(uint value)` 代替。
- `ReadPackedUInt64(ulong value)` 使用 `ReadUInt64(ulong value)` 代替。
- `ReadBoolean` 重命名为 `ReadBool`。
- `ReadInt16` 重命名为 `ReadShort`。
- `ReadInt32` 重命名为 `ReadInt`。
- `Readint64` 重命名为 `ReadLong`。
- `ReadSingle` 重命名为 `ReadFloat`。

### NetworkWriter <a href="#networkwriter" id="networkwriter"></a>

- `Write(bool value)`\
  &#x20;使用 `WriteBool` 代替。
- `Write(byte value)`\
  &#x20;使用 `WriteByte` 代替。
- `Write(sbyte value)`\
  &#x20;使用 `WriteSByte` 代替。
- `Write(short value)`\
  &#x20;使用 `WriteShort` 代替。
- `Write(ushort value)`\
  &#x20;使用 `WriteUShort` 代替。
- `Write(int value)`\
  &#x20;使用 `WriteInt` 代替。
- `Write(uint value)`\
  &#x20;使用 `WriteUInt` 代替。
- `Write(long value)`\
  &#x20;使用 `WriteLong` 代替。
- `Write(ulong value)`\
  &#x20;使用 `WriteULong` 代替。
- `Write(float value)`\
  &#x20;使用 `WriteFloat` 代替。
- `Write(double value)`\
  &#x20;使用 `WriteDouble` 代替。
- `Write(decimal value)`\
  &#x20;使用 `WriteDecimal` 代替。
- `Write(string value)`\
  &#x20;使用 `WriteString` 代替。
- `Write(char value)`\
  &#x20;使用 `WriteChar` 代替。
- `Write(Vector2 value)`\
  &#x20;使用 `WriteVector2` 代替。
- `Write(Vector2Int value)`\
  &#x20;使用 `WriteVector2Int` 代替。
- `Write(Vector3 value)`\
  &#x20;使用 `WriteVector3` 代替。
- `Write(Vector3Int value)`\
  &#x20;使用 `WriteVector3Int` 代替。
- `Write(Vector4 value)`\
  &#x20;使用 `WriteVector4` 代替。
- `Write(Color value)`\
  &#x20;使用 `WriteColor` 代替。
- `Write(Color32 value)`\
  &#x20;使用 `WriteColor32` 代替。
- `Write(Guid value)`\
  &#x20;使用 `WriteGuid` 代替。
- `Write(Transform value)`\
  &#x20;使用 `WriteTransform` 代替。
- `Write(Quaternion value)`\
  &#x20;使用 `WriteQuaternion` 代替。
- `Write(Rect value)`\
  &#x20;使用 `WriteRect` 代替。
- `Write(Plane value)`\
  &#x20;使用 `WritePlane` 代替。
- `Write(Ray value)`\
  &#x20;使用 `WriteRay` 代替。
- `Write(Matrix4x4 value)`\
  &#x20;使用 `WriteMatrix4x4` 代替。
- `Write(NetworkIdentity value)`\
  &#x20;使用 `WriteNetworkIdentity` 代替。
- `Write(GameObject value)`\
  &#x20;使用 `WriteGameObject` 代替。
- `Write(byte[] buffer, int offset, int count)`\
  &#x20;使用 `WriteBytes` 代替。
- `WritePackedInt32(int value)`\
  &#x20;使用 `WriteInt32(int value)` 代替
- `WritePackedUInt32(uint value)`\
  &#x20;使用 `WriteUInt32(uint value)` 代替
- `WritePackedUInt64(ulong value)`\
  &#x20;使用 `WriteUInt64(ulong value)` 代替

### RemoteCallHelper

- 已重命名为 `RemoteProcedureCalls`（远程过程调用）。
- `CmdDelegate`（命令委托）已重命名为 `RemoteCallDelegate`（远程调用委托）。
- `MirrorInvokeType`（Mirror 调用类型）已重命名为 `RemoteCallType`（远程调用类型）。

### Transport <a href="#transport" id="transport"></a>

- `GetConnectionInfo(int connectionId, out string address)`\
  &#x20;改用 `ServerGetClientAddress(int connectionId)`（服务器获取客户端地址）。
- `GetMaxBatchSize`（获取最大批处理大小）已重命名为 `GetMaxPacketSize`（获取最大数据包大小）。
- `ClientSend(int channelId, ArraySegment segment)`

  改用 `ClientSend(segment, channelId)`（客户端发送）。

- `ServerSend(int connectionId, int channelId, ArraySegment segment)`

  改用 `ServerSend(connectionId, segment, channelId)`（服务器发送）。

### Telepathy Transport <a href="#telepathytransport" id="telepathytransport"></a>

- `MaxMessageSize`（最大消息大小）\
  &#x20;改用 `MaxMessageSizeFromClient`（来自客户端的最大消息大小）或 `MaxMessageSizeFromServer`（来自服务器的最大消息大小）。

### Fallback Transport

- 已移除。

### Utils

- `Version`（版本）枚举已移除。
- `DefaultReliable`（默认可靠）已重命名为 `Reliable`（可靠）。
- `DefaultUnreliable`（默认不可靠）已重命名为 `Unreliable`（不可靠）。
