# 弃用

Unity Networking(UNet)的某些功能因各种原因从 Mirror 中删除或修改。 此页面将标识所有更改和删除的功能、属性和方法，更改或删除的原因以及可能的替代方案。

{% hint style="info"%}
**注意：**本文档中的某些更改可能适用于即将发布到资产存储的版本。
联系我们

## 匹配空间和主机迁移<a href="#match-namespace--host-migration" id="match-namespace--host-migration"></a>

作为 Unity 服务的一部分，整个命名空间已被删除。 它一开始就工作得不好，而且作为核心网络包的一部分非常复杂。 我们预计这一点，以及其他后端服务，将通过集成到 Mirror 的独立应用程序提供。

## 网络服务器简单<a href="#network-server-simple" id="network-server-simple"></a>

这是太复杂和不切实际的维护什么小它做，并被删除。 有更简单的方法来制作一个基本的监听服务器，无论是否使用我们的传输。

## 治疗床合作<a href="#couch-co-op" id="couch-co-op"></a>

核心网络通过移除这个容易实现的目标而得到了极大的简化。 这是错误的，太复杂了，不值得修复。 对于那些需要类似的东西的人来说，考虑定义一个不可见的播放器预制件作为一个消息管道，产生具有客户端权限的实际播放器预制件。 所有的输入都将通过管道预制件来控制玩家对象。

## 消息类型<a href="#message-types" id="message-types"></a>

`MsgType`枚举已删除。 所有消息类型都是动态生成的。 请改用`Send`。

## 网络转型<a href="#network-transform" id="network-transform"></a>

[网络变换](../components/network-transform/)被完全替换，因此它只同步位置，旋转和缩放，具有这些以及是否插值的控制选项，并使用快照插值。 它也有一堆虚方法，还有一个[脚本模板](script-templates.md)用于创建自己的派生版本。 为了支持单独的[网络刚体](../components/network-rigidbody.md)组件，删除了刚体支持。

## 网络动画师<a href="#network-animator" id="network-animator"></a>

[Network Animator](../components/network-animator.md)也得到了简化，因为它将所有 Animator 参数批处理到单个更新消息中。

## SyncVar 钩参数<a href="#syncvar-hook-parameters" id="syncvar-hook-parameters"></a>

[SyncVar](../guides/synchronization/syncvars.md)属性值现在会在调用钩子之前更新，钩子现在需要两个与属性类型相同的参数：`oldValue`和`newValue`

## 同步列表 CT<a href="#syncliststruct" id="syncliststruct"></a>

请改用[SyncList](../guides/synchronization/synclists.md)。

## SyncList 类

- `SyncListString`已替换为`SyncList<string>`。
- `SyncListFloat`已被`SyncList<float>`取代。
- `SyncListInt`已被`SyncList<int>`取代。
- `SyncListUInt`已替换为`SyncList<uint>`。
- `SyncListBool`被`SyncList<bool>`取代。

如需详细信息，请参阅[文件](../guides/synchronization/synclists.md)。

## SyncList 操作<a href="#synclist-operations" id="synclist-operations"></a>

- `OP_REMOVE`替换为`OP_REMOVEAT`
- `OP_DIRTY`已替换为`OP_SET`

如需详细信息，请参阅[文件](../guides/synchronization/synclists.md)。

## SyncDictionary 操作<a href="#syncidictionary-operations" id="syncidictionary-operations"></a>

- `OP_DIRTY`已替换为`OP_SET`

如需详细信息，请参阅[文件](../guides/synchronization/syncdictionary.md)。

## SyncObject

- 这是一个类而不是一个接口。
- `Flush`-使用`ClearChanges`代替。

## 服务质量标志<a href="#quality-of-service-flags" id="quality-of-service-flags"></a>

在传统的 UNet 中，QoS 标志用于确定数据包如何到达远端。 例如，如果您需要在队列中对数据包进行优先级排序，您可以指定一个高优先级标志，然后 Unity LLAPI 将接收并适当处理该标志。 不幸的是，这导致了传输层的大量额外工作，并且由于依赖于太多魔法的错误代码，一些 QoS 标志不能按预期工作。

在 Mirror 中，QoS 标志被"Channels"系统取代。 虽然默认传输[Telepathy](../transports/telepathy-transport.md)根本不使用通道，因为它是基于 TCP 的，但其他传输，如[Ignorance](../transports/ignorance.md)和[LiteNetLib](../transports/litenetlib-transport.md)，确实支持它们。

当前定义的通道为：

- `Channels.Reliable = 0`
- `Channels.Unreliable = 1`

## 按类别更改<a href="#changes-by-class" id="changes-by-class"></a>

### NetworkManager<a href="#networkmanager" id="networkmanager"></a>

- `NetworkConnection`在许多地方被`NetworkConnectionToClient`取代。
- `networkPort`\
  作为分离传输至组件的一部分而删除。 并非所有的传输都使用端口，但是那些确实有端口的传输都有一个字段。更多信息请参见[传输](../transports/)。
- `IsHeadless()`\
  使用编译器符号`UNITY_SERVER`代替。
- `ConfigureServerFrameRate`已重命名为`ConfigureHeadlessFrameRate`。
- `client`\
  直接使用 NetworkClient，它很快就会变成静态的。 例如，使用`NetworkClient.Send(message)`而不是`NetworkManager.client.Send(message)`。
- `IsClientConnected()`\
  请改用静态属性`NetworkClient.isConnected`。
- `onlineScene`和`offlineScene`\
  &#x20;这些函数现在存储完整的路径，因此使用 SceneManager.GetActiveScene().path 代替。
- `OnStartClient(NetworkClient client)`\
  &#x20;改用 OnStartClient()，因为所有`NetworkClient`方法现在都是静态的。
- `OnClientChangeScene(string newSceneName)`\
  &#x20;将其改为`OnClientChangeScene(string newSceneName, SceneOperation sceneOperation, bool customHandling)`。
- `OnClientChangeScene(string newSceneName, SceneOperation sceneOperation)`\
  &#x20;将其改为`OnClientChangeScene(string newSceneName, SceneOperation sceneOperation, bool customHandling)`。
- `OnServerAddPlayer(NetworkConnection conn, AddPlayerMessage extraMessage)`\
  &#x20;请改用`OnServerAddPlayer(NetworkConnection conn)`。 有关详细信息，请参阅[自定义玩家生成指南](../guides/gameobjects/custom-character-spawning.md)。
- `OnServerRemovePlayer(NetworkConnection conn, NetworkIdentity player)`\
  &#x20;使用`NetworkServer.RemovePlayerForConnection(NetworkConnection conn, GameObject player, bool keepAuthority = false)`代替。
- `OnServerError(NetworkConnection conn, int errorCode)`

  Replaced with `OnServerError(NetworkConnection conn, Exception exception)`.

- `OnClientError(NetworkConnection conn, int errorCode)`

  Replaced with `OnClientError(Exception exception)`.

- 已删除`disconnectInactiveConnections`和`disconnectInactiveTimeout`。
- OnClient\* 虚拟方法不再采用`NetworkConnection`参数。 从重写中删除该参数，并在代码中使用`NetworkClient.connection`。
- `serverTickRate`重命名为`sendRate`。
- `serverTickInterval`已移动到`NetworkServer`。

### 网络管理器 HUD

- `showGUI`已删除。

  Disable the component instead.

### 网络室管理器<a href="#networkroommanager" id="networkroommanager"></a>

- `NetworkConnection`在许多地方被`NetworkConnectionToClient`取代。
- `OnRoomServerCreateGamePlayer(NetworkConnection conn)`\
  &#x20;请改用`OnRoomServerCreateGamePlayer(NetworkConnection conn, GameObject roomPlayer)`。
- `OnRoomServerSceneLoadedForPlayer(GameObject roomPlayer, GameObject gamePlayer)`\
  &#x20;请改用`OnRoomServerSceneLoadedForPlayer(NetworkConnection conn, GameObject roomPlayer, GameObject gamePlayer)`。
- 客户端虚拟方法不再采用`NetworkConnection`参数。

  Use `NetworkClient.connection` within your overrides.

### 网络身份<a href="#networkidentity" id="networkidentity"></a>

- `clientAuthorityOwner`\
  &#x20;使用 connectionToClient 代替
- `GetSceneIdenity`\
  &#x20;使用`GetSceneIdentity`代替(原始名称中的排印错误)
- `RemoveClientAuthority(NetworkConnection conn)`\
  &#x20;不再需要 NetworkConnection 参数，并且不返回任何内容
- `spawned`字典

  This has been split up to `NetworkServer.spawned` and `NetworkClient.spawned` dictionaries.

- 本地播放器权限复选框\
  &#x20;不再需要此复选框，并且我们简化[了权限](../guides/authority.md)在 Mirror 中的工作方式。

### 网络行为<a href="#networkbehaviour" id="networkbehaviour"></a>

- `NetworkConnection`在许多地方被`NetworkConnectionToClient`取代。
- `sendInterval`属性\
  &#x20;请改用`NetworkBehaviour.syncInterval`字段。 也可以在检查器中修改。
- `List m_SyncObjects`\
  &#x20;请改用`List syncObjects`。
- `OnSetLocalVisibility(bool visible)`\
  &#x20;请改为使用`OnSetHostVisibility(bool visible)`。
- `OnRebuildObservers`、`OnCheckObserver`和`OnSetHostVisibility`已移至名为`NetworkVisibility`的单独类
- `NetworkBehaviour.OnNetworkDestroy`已重命名为`NetworkBehaviour.OnStopClient`。
- `getSyncVarHookGuard`重命名为`GetSyncVarHookGuard`。
- `setSyncVarHookGuard`-重命名为`SetSyncVarHookGuard`。
- `SetDirtyBit`-使用`SetSyncVarDirtyBit`代替。
- `[命令]`属性参数`ignoreAuthority`替换为`requiresAuthority`。
- `[ClientRpc]`属性参数`includeOwner`替换为`excludeOwner`。
- `hasAuthority`重命名为`isOwned`。

### 网络连接<a href="#networkconnection" id="networkconnection"></a>

- `hostId`\
  &#x20;删除是因为自从我们将 LLAPI 作为默认值删除后就不再需要它了。 对于常规连接，它始终为 0，对于本地连接，它始终为-1。 使用`connection.GetType() == typeof(NetworkConnection)`检查它是常规连接还是本地连接。
- `isConnected`\
  &#x20;因为这毫无意义。 `NetworkConnection`始终处于连接状态。
- `InvokeHandlerNoData(int msgType)`\
  &#x20;使用`InvokeHandler`代替。
- `playerController`\
  &#x20;重命名为`identity`，因为它就是：连接的`NetworkIdentity`。
- `RegisterHandler(short msgType, NetworkMessageDelegate handler)`\
  &#x20;请改用`NetworkServer.RegisterHandler()`或`NetworkClient.RegisterHandler()`。
- `UnregisterHandler(short msgType)`\
  &#x20;请改用`NetworkServer.UnregisterHandler()`或`NetworkClient.UnregisterHandler()`。
- `Send(int msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;使用`Send(msg, channelId)`代替。
- `clientOwnedObjects`重命名为`owned`。

### 网络服务器<a href="#networkserver" id="networkserver"></a>

- `NetworkConnection`在许多地方被`NetworkConnectionToClient`取代。
- `FindLocalObject(uint netId)`\
  &#x20;请改用`NetworkServer.spawned[netId].gameObject`。
- `RegisterHandler(int msgType, NetworkMessageDelegate handler)`\
  &#x20;使用`RegisterHandler(T msg)`代替。
- `RegisterHandler(MsgType msgType, NetworkMessageDelegate handler)`\
  &#x20;使用`RegisterHandler(T msg)`代替。
- `RegisterHandler(Action handler, bool requireAuthentication = true)`

  Use `RegisterHandler(Action<NetworkConnection, T), requireAuthentication = true)` instead.

- `UnregisterHandler(int msgType)`\
  &#x20;使用`UnregisterHandler(T msg)`代替。
- `UnregisterHandler(MsgType msgType)`\
  &#x20;使用`UnregisterHandler(T msg)`代替。
- `SendToAll(int msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;使用`SendToAll(T msg, int channelId = Channels.Reliable)`代替。
- `SendToClient(int connectionId, int msgType, MessageBase msg)`\
  使用`NetworkConnection.Send(T msg, int channelId = Channels.Reliable)`代替。
- `SendToClient(int connectionId, T msg)`\
  使用`NetworkConnection.Send(T msg, int channelId = Channels.Reliable)`代替。
- `SendToClientOfPlayer(NetworkIdentity identity, int msgType, MessageBase msg)`\
  使用`identity.connectionToClient.Send(T message, int channelId = Channels.Reliable)`代替。
- `SendToReady(NetworkIdentity identity, short msgType, MessageBase msg, int channelId = Channels.Reliable)`\
  &#x20;请改用`identity.connectionToClient.Send()`。
- `SendToReady(NetworkIdentity identity, T message, bool includeOwner = true, int channelId = Channels.Reliable)`

  Renamed to `SendToReadyObservers`.

- `SpawnWithClientAuthority(GameObject obj, GameObject player)`\
  &#x20;使用`Spawn(GameObject obj, GameObject player)`代替。
- `SpawnWithClientAuthority(GameObject obj, NetworkConnection ownerConnection)`\
  &#x20;使用`Spawn(GameObject obj, NetworkConnection ownerConnection)`代替。
- `SpawnWithClientAuthority(GameObject obj, Guid assetId, NetworkConnection ownerConnection)`\
  &#x20;使用`Spawn(GameObject obj, Guid assetId, NetworkConnection ownerConnection)`代替。
- 已删除`disconnectInactiveConnections`和`disconnectInactiveTimeout`。
- `NoConnections`已重命名为`NoExternalConnections`。
- `DisconnectAllExternalConnections`/`DisconnectAllConnections`

  Use `DisconnectAll` instead.

- 为清晰`OnError`重命名为`OnTransportError`。

### 网络客户端<a href="#networkclient" id="networkclient"></a>

- `NetworkClient singleton`\
  &#x20;直接使用`NetworkClient`。 Singleton 不再需要了，因为现在所有的函数都是静态的。
  &#x20;示例：`NetworkClient.Send(message)`而不是`NetworkClient.singleton.Send(message)`。
- `allClients`\
  &#x20;直接使用`NetworkClient`代替。 永远只有一个客户。
- `GetRTT()`\
  &#x20;请改用`NetworkTime.rtt`。
- `readyConnection`

  Use `connection` instead.

- `isLocalClient`

  Use `isHostClient` instead.

- `DisconnectLocalServer()`

  Use `NetworkClient.Disconnect()` instead.

- `RegisterHandler(int msgType, NetworkMessageDelegate handler)`\
  &#x20;使用`RegisterHandler(T msg)`代替。
- `RegisterHandler(MsgType msgType, NetworkMessageDelegate handler)`\
  &#x20;使用`RegisterHandler(T msg)`代替。
- `注册表(Action<NetworkConnection，T>  handler, bool requireAuthentication = true)`

  Use `RegisterHandler(Action<T> handler, bool requireAuthentication = true)` instead.

- `UnregisterHandler(int msgType)`\
  &#x20;使用`UnregisterHandler(T msg)`代替。
- `UnregisterHandler(MsgType msgType)`\
  &#x20;使用`UnregisterHandler(T msg)`代替。
- `Ready(NetworkConnection conn)`

  Use `Ready()` without the `NetworkConnection` parameter instead.

- `Send(short msgType, MessageBase msg)`\
  &#x20;使用`Send(T msg, int channelId = Channels.Reliable)`，而不使用消息 ID
- `ShutdownAll()`\
  &#x20;请`Shutdown()`。 只有一个客户。
- 为清晰`OnError`重命名为`OnTransportError`。

### 客户端场景<a href="#clientscene" id="clientscene"></a>

- 合并到`NetworkClient`。

### 网络场景

- 被[场景兴趣管理](../interest-management/scene.md)取代。

### 网络邻近度

- [空间散列](../interest-management/spatial-hashing.md)/[距离](../interest-management/distance.md)兴趣管理(Space Hash / Distance Interest Management)

### 网络比赛

- 替换为网络匹配，需要[匹配兴趣](../interest-management/match.md)管理。

### 网络所有者

- 由网络团队取代，需要[团队兴趣管理](../interest-management/team.md)。

### 网络认证符

- `NetworkConnection`在许多地方被`NetworkConnectionToClient`取代。
- `OnClientAuthenticate`不再采用`NetworkConnection`参数。\
  根据需要使用`NetworkClient.connection`。
- `OnClientAuthenticated`事件不再采用`NetworkConnection`参数。\
  根据需要使用`NetworkClient.connection`。
- 客户端消息处理程序中不再使用`NetworkConnection`。

  Use `NetworkClient.connection` within your handlers instead.

- `ClientAccept`和`ClientReject`不再需要`NetworkConnection`参数。

### 网络时间

- `NetworkTime.timeVar`已重命名为`timeVariance`。
- `NetworkTime.timeSd`已重命名为`timeStandardDeviation`。
- 已将`NetworkTime.rttVar`重命名为`rttVariance`。
- 已将`NetworkTime.rttSd`重命名为`rttStandardDeviation`。

### 运输

- `activeTransport`重命名为`active`。

### 消息<a href="#messages" id="messages"></a>

简单类型的基本消息都被删除了，因为不必要的膨胀。 您可以创建自己的消息类。

- `StringMessage`
- `ByteMessage`
- `BytesMessage`
- `IntegerMessage`
- `DoubleMessage`
- `EmptyMessage`

NetworkMessage 在所有情况下都需要结构体-不再支持类

### 网络阅读器<a href="#networkreader" id="networkreader"></a>

- `读取(字节 buffer, int offset, int count)`\
  &#x20;请改用`ReadBytes`。
- `ReadPackedInt32(int value)`使用`ReadInt32(int value)`代替。
- `ReadPackedUInt32(uint value)`使用`ReadUInt32(uint value)`代替。
- `ReadPackedUInt64(ulong value)`使用`ReadUInt64(ulong value)`代替。
- `ReadBoolean`重命名为`ReadBool`。
- `ReadInt16`重命名为`ReadShort`。
- `ReadInt32`重命名为`ReadInt`。
- `Readint64`重命名为`ReadLong`。
- `ReadSingle`重命名为`ReadFloat`。

### 网络作家<a href="#networkwriter" id="networkwriter"></a>

- `Write(bool value)`\
  &#x20;使用`WriteBool`代替。
- `Write(byte value)`\
  &#x20;使用`WriteByte`代替。
- `Write(sbyte value)`\
  &#x20;请改用`WriteSByte`。
- `Write(short value)`\
  &#x20;使用`WriteShort`代替。
- `Write(ushort value)`\
  &#x20;使用`WriteUShort`代替。
- `Write(int value)`\
  &#x20;使用`WriteInt`代替。
- `Write(uint value)`\
  &#x20;使用`WriteUInt`代替。
- `Write(long value)`\
  &#x20;使用`WriteLong`代替。
- `Write(ulong value)`\
  &#x20;使用`WriteULong`代替。
- `Write(float value)`\
  &#x20;使用`WriteFloat`代替。
- `Write(double value)`\
  &#x20;使用`WriteDouble`代替。
- `Write(decimal value)`\
  &#x20;使用`WriteDecimal`代替。
- `Write(string value)`\
  &#x20;使用`WriteString`代替。
- `Write(char value)`\
  &#x20;使用`WriteChar`代替。
- `Write(Vector2 value)`\
  &#x20;使用`WriteVector2`代替。
- `Write(Vector2Int value)`\
  &#x20;使用`WriteVector2Int`代替。
- `Write(Vector3 value)`\
  &#x20;使用`WriteVector3`代替。
- `Write(Vector3Int value)`\
  &#x20;使用`WriteVector3Int`代替。
- `Write(Vector4 value)`\
  &#x20;使用`WriteVector4`代替。
- `Write(Color value)`\
  &#x20;使用`WriteColor`代替。
- `Write(Color32 value)`\
  &#x20;使用`WriteColor32`代替。
- `Write(Guid value)`\
  &#x20;请改用`WriteGuid`。
- `Write(Transform value)`\
  &#x20;请改用`WriteTransform`。
- `Write(Quaternion value)`\
  &#x20;请改用"`WriteQuaternion`。
- `Write(Rect value)`\
  &#x20;使用`WriteRect`代替。
- `Write(Plane value)`\
  &#x20;请改用`WritePlane`。
- `Write(Ray value)`\
  &#x20;使用`WriteRay`代替。
- `Write(Matrix4x4 value)`\
  &#x20;使用`WriteMatrix4x4`代替。
- `Write(NetworkIdentity value)`\
  &#x20;请改用`WriteNetworkIdentity`。
- `Write(GameObject value)`\
  &#x20;使用`WriteGameObject`代替。
- `写(字节 buffer, int offset, int count)`\
  &#x20;请使用`WriteBytes`代替。
- `WritePackedInt32(int value)`\
  &#x20;使用`WriteInt32(int value)`代替
- `WritePackedUInt32(uint value)`\
  &#x20;使用`WriteUInt32(uint value)`代替
- `WritePackedUInt64(ulong value)`\
  &#x20;使用`WriteUInt64(ulong value)`代替

### RemoteCallHelper

- 已重命名为`RemoteProcedureCalls`。
- `CmdDelegate`重命名为`RemoteCallDelegate`。
- 已`MirrorInvokeType`重命名为`RemoteCallType`。

### 运输<a href="#transport" id="transport"></a>

- `GetConnectionInfo(int connectionId, out string address)`\
  &#x20;使用`ServerGetClientAddress(int connectionId)`代替。
- `GetMaxBatchSize`重命名为`GetMaxPacketSize`。
- `ClientSend(int channelId, ArraySegment segment)`

  Use `ClientSend(segment, channelId)` instead.

- `ServerSend(int connectionId, int channelId, ArraySegment segment)`

  Use `ServerSend(connectionId, segment, channelId)` instead.

### 心灵感应传送<a href="#telepathytransport" id="telepathytransport"></a>

- `MaxMessageSize`\
  &#x20;请改用`MaxMessageSizeFromClient`或`MaxMessageSizeFromServer`。

### 后备运输

- 这一点已被删除。

### Utils

- 已删除`Version`枚举。
- `DefaultReliable`重命名为`Reliable`。
- `DefaultUnreliable`重命名为`Unreliable`。
