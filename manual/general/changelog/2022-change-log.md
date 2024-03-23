# 2022 变更日志

{% hint style="info"%}
镜像将在每月初发布到[资产存储中](https://assetstore.unity.com/packages/tools/network/mirror-129321)，除非某些关键问题导致延迟。
联系我们

{% hint style="info"%}
镜像使用语义版本控制，此处显示的版本是发布到资产存储的版本，偶尔会在存储提交之间的月中发生重大版本冲突，因此此处不单独显示。
联系我们

## v71.0.0 -- 2022 年 12 月 31 日

- 新增：`NetworkClient.activeHost`新增为静态。
- 新增：同步\* 集合现在支持客户端到服务器同步方向。
- 新增：网络管理器 HUD 现在有一个 StopClient 按钮用于主机模式。
  - 这将使服务器以"仅服务器"模式运行，并删除主机客户端。
- 修正：KCP 更新到 1.26 版本。
- 修正：`NetworkManager.OnStopClient`现在检查客户端是否已启动。
- 修正：`NetworkManager.OnClientSceneChanged`现在在调用`NetworkClient.Ready`之前检查已验证的客户端。
- 修正：`NetworkManager.mode`现在在调用 OnStopClient 之前设置为`offline`以防止递归。
- 修正：改进了`SimpleWebSocket`日志记录。
- 修正：`NetworkManager`模板现在在 Awake 覆盖中重置单例。
- 修正：`NetworkDiscovery`模板现在正确地将消息定义为结构。
- 修正：NetworkTransform / NetworkTransformReliable 现在检查 UpdateServer 中的 null connectionToClient。
  - 当玩家对象被场景改变放弃时，这会修复 NRE。
- 修正：` NetworkTransformChild``AddComponentMenu `属性重置，因为它已经过时。
- 修正：`NetworkRigidbody2D`现在有`HelpURL`属性。
- 修正：`NetworkServer`日志现在有游戏对象引用，便于调试。
- 固定：FPS 脚本有错误的命名空间.
- 修正：网络验证器现在使用`NetworkClient.Send`，这样我们就不会绕过空检查。
- 修正：`NetworkClient.owned`现在在 OnDestroy 中正确清理。
- 修正：`NetworkIdentity.spawned`现在在 OnDestroy 中正确清理。
- 修复：`SpawnObject`缺少对已生成身份的检查。
  - 这将清除 Additive Scenes 实现中的警告。
- 更改：`NetworkServer.localConnection`现在是`LocalConnectionToClient`类型。
- 更改：NetworkManager 的 OnStartServer 和 OnStartHost 现在在启动序列中稍后调用。
- 更改：`NetworkRoomManager`不再做最大连接检查.`网络服务器已经这样做了。
- 问题：`NetworkServer.localClientActive`已重命名为`activeHost`。
- **突破**：`NetworkTransform`和`NetworkTransformReliable`现在继承自一个新`NetworkTransformBase`。

## v70.0.0 -- 2022 年 12 月 9 日

- 添加：恢复对 Unity 2019 LTS 的支持
  - 不保证这会持续多久。 Unity 不再支持它，代码债务增加。
- 修正：KCP 更新到 1.23 版本。
- 修正：示例已更新，可与此版本配合使用。
- 修正：利息管理现在在几个地方使用`TryGetValue`。
- 修正：心灵感应不再错误地调用 OnDisconnected 两次。
- 修复：NetworkClient 重新启动时清除传输处理程序。
- 修正：多路传输的几个错误被修正。
- 修正：Weaver 现在支持`NetworkConnectionParameter`的继承。
- 修正：客户端到服务器的同步变量没有发送到客户端。
- 修正：改进了传输中的日志记录。
- 更改：NetworkManager.OnClientDisconnect 现在是一个空的虚拟方法。
  - 脚本模板也已更新。
- **突破**：`NetworkTransform`可靠和不可靠
  - 不可靠的版本取代旧的`NetworkTransform`。
  - `SyncInterval`现在由` NetworkServer``sendRate `(在 Network Manager 中设置)管理。
  - `NetworkTransformChild`删除.使用网络转换并将目标设置为子对象。
  - `clientAuthority`已废弃，以支持在先前版本中实现的`syncDirection`。

{% hint style="info"%}
在此版本中恢复了对 Unity 2019 LTS 的支持，但我们无法保证随着代码债的增加，我们能够保持兼容性多久。
联系我们

## v69.0.0 -- 2022 年 11 月 10 日

{% hint style="info"%}
此版本已作为 2022-10 发布到资产存储。
联系我们

- 添加：CCU 测试示例。
- 添加：`NetworkStatistics`组件。
- 修正：`NetworkRigidbody`现在使用双倍时间来保持长时间运行的精度。
- 修正：`NetworkLerpRigidbody`现在使用双倍时间来保持长时间运行的精度。
- 修正：`NetworkWriter.WriteString()`现在确保写入前的容量。
- 更改：创建工具文件夹并进行了一些重新排列。

## v68.0.0 -- 2022 年 10 月 21 日

{% hint style="info"%}
此版本作为 2022-09 发布到资产存储。
联系我们

- 新增：快照插值示例。
- 新增：`NetworkPingDisplay`现在有可配置的大小。
- 新增：`NetworkManager.GetStartPosition`现在是虚拟的，因此可以被覆盖。
- 新增：网络管理器现在有一个自动连接选项，用于无头客户端/ CCU 测试。
- 增加：`NetworkConnection`现在在服务器和客户端上维护`owned`HashSet。
- 增加：`NetworkBehaviour`现在有`SyncDirection`，使客户端权限组件更容易。
  - 注意：此功能将继续发展，并可能在未来版本中更改。
- 修正：基准测试，聊天和坦克的例子得到了改进。
- 修复：网络管理器脚本模板已更新。
- 修正：`NetworkBehaviour`日志记录被改进。
- 修正：`SimpleWebTransport`头查找现在不区分大小写。
- 固定：空间哈希利息管理。 现在可以正确计算可视范围。
- 修正：读/写操作不再分配垃圾。
- 修正：心灵感应`ClientDisconnect`现在调用`Disconnect`事件正确。
- 固定`NetworkServer.Spawn`现在检查和警告重复。
- 修正：`NetworkIdentity`现在只在编辑器上下文中检查`Application.isPlaying`。
- 修正：网络统计属性公开，以便其他组件可以访问它们。
- 修正：`InterestManagement.Awake`现在是`protected virtual`，所以它可以被覆盖。
- 修正：`componentIndex`的`NetworkIdentity`序列化减少到\~1 位而不是 1 字节。
- 修正：`NetworkBehaviour`头的序列化减少到 1 字节而不是 4 字节。
- 固定：`NetworkClient`现在重置时间插值每当主机连接或重新连接。
- **更改**：`NetworkBehaviour.ComponentIndex`从`int`减少到`byte`。
- **更改**：快照插值重构为版本 2。
- **更改**：延迟模拟`latencySpike`重命名为`jitter`。
- **变更**：快照插值分为时间+数值插值。
- **中断**：运输文件夹被向上移动，各种运输工具被移动到该文件夹中。
- **中断**：将 Windows 文件夹重命名为 Core。
- **突破**：`NetworkIdentity`/` NetworkBehaviour``OnSerialize `不再返回`bool`。
- **突破**：`NetworkBehaviour.SerializeSyncVars`不再返回`bool`。
- **中断**：`SpawnMessage`中`assetId`从`Guid`更改为`uint`。
- **中断**：`OnError(Exception)`现在是`OnError(TransportError, string)`，其中`TransportError`是枚举，string 是传输提供的自由文本。
- **中断**：为清晰`NTSnapshot`重命名为`TransformSnapshot`。
- **突破**：`NetworkTime`被重写，以使用`NetworkClient`的快照插值时间轴。
- **问题**：`NetworkManager.serverTickRate`已重命名为`sendRate`。
- **问题**：`NetworkManager.serverTickinterval`已移动到`NetworkServer`。
- **Objective**：`NetworkTransport.activeTransport`重命名为`active`。
- **Objective**：为清晰起见`NetworkBehavior.hasAuthority`重命名为**isOwned**。
- \*\*\*\*为了清晰起见，`NetworkConnection.clientOwnedObjects`重命名为`owned`。

## v67.1.0 -- 2022 年 5 月 5 日

- 修正：KCP 更新到 1.19 版本。
- 修正：改进了团队利益管理的逻辑。`OnDestroyed`
- 修正：更新了各种网络验证器中的评论。
- 修正：基准测试的例子被改进，所以移动围绕原点。
- 修正：Cmd/Rpc 带宽从 4 字节减少到 2 字节。
- 修正：`Texture2D`现在包括尺寸，正确读取`Color32`并支持空值。
- 修正：`NetworkServer.SendToObservers`现在正确使用`NetworkConnectionToClient`。
- 修正：`NetworkClient`现在检查并警告重复的场景 ID。
- **更改**：为清晰起见`NetworkClient`/` NetworkServer``OnError `重命名为`OnTransportError`
- **打破**：过时的被删除。

## v66.0.9 -- 2022-Apr-17

{% hint style="warning"%}
**注意：**这是支持 Unity 2019 LTS 的最后一个镜像版本，因为它已经达到 Unity 的生命周期。 支持已恢复在镜像 70.0.0 以上。
联系我们

- 新增：[网络统计](../../components/network-statistics.md)组件，显示每秒发送和接收的消息和数据包。
- 已添加：为 Unity 2019 和 2020 兼容性添加`Queue.TryDequeue`扩展。
- 修正：`NetworkClient.ChangeOwner`现在使用`isLocalPlayer`标志来检查是否应该调用`OnStopLocalPlayer`。
- 修正：`ReadNetworkBehavior`现在可以正确读取客户端上的数据，即使客户端缺少关联`NetworkIdentity`。
- 修正：`NetworkClient.SpawnPrefab`现在在使用注册的预制列表之前而不是之后正确地查找自定义的衍生处理程序。
- 修正：`NetworkWriterExtensions`现在使用`WriteArray`而不是`Write`。
- 修正：尝试序列化预制件和非衍生的游戏对象抛出一个更明显的异常。
- 修正：`DestroyAllClientObjects`现在也会在调用 unspawn 处理程序后重置。
- 修正：在主机模式下调用`StopClient`不再重置`nextNetId`。
- 修正：在主机模式下调用`StopClient`不再破坏客户端上的对象。
- 已修正：现在应该可以在 Host 模式下调用 StopClient，使服务器处于运行的"Server Only"状态，保持所有客户端连接并正常运行。
- 修正：`AggressiveInlining`属性在许多地方增加，以提高性能。
- **更改：**简化`NetworkWriter`/`NetworkReader`
- **更改：**`PooledNetworkReader`/`PooledNetworkWriter`重命名为`NetworkReaderPooled`/`NetworkWriterPooled`。
- **已更改：**传输基类移动到了"传输"文件夹，传输文件夹重命名为"传输"

## v65.0.0 -- 2022-Mar-09

- 新增：Command / ClientRpc / TargetRpc 现在可以重载。
- 添加：网络行为现在有`OnStopLocalPlayer`虚拟方法，在`OnStopClient`之前调用。 脚本模板也已更新。
- 添加：网络管理器模板覆盖`singleton`，以消除需要将网络管理器的单例转换为派生类类型。
- 新增：支持通用`NetworkBehaviour<T>`子类。
- 修正：网络阅读器/作家使用 blittable 读/写在可能的情况下。
- 修正：Android 兼容读/写 blittable。
- 修复：Android 多播发现。
- 修正：`NetworkTransformChild`现在正确同步初始状态时，产卵。
- 修正：延迟模拟现在使用`Time.unscaledTime`。
- `SimpleWebsocketTransport`已更新，以修复一些错误，包括与 Unity 2021.x 的兼容性，以及处理大于 64K 的消息。
- **更改**：一堆虚拟方法现在采用`NetworkConnectionToClient`而不是`NetworkConnection`。
- **已更改**：`RemoteCallHelper`重命名为`RemoteProcedureCalls`。
- **更改**：`CmdDelegate`重命名为`RemoteCallDelegate`。
- **已更改**：`MirrorInvokeType`重命名为`RemoteCallType`。
- **更改：**`NetworkManager.clientLoadedScene`现在受到`protected`。
- **反对意见**：`NetworkTransform`现在有`CmdTeleport`/`RpcTeleport`的重载，带有四元数旋转，反对单独`CmdTeleportAndRotate`/`RpcTeleportAndRotate`方法，通过实现上面提到的 Command / ClientRpc 重载。
- **对象：**实验网络变换组件。 用普通的吧。

## v57.0.0 -- 2022 年 1 月 12 日

- 修正：KCP 更新到 1.17 版本。
- 修正：使用读/写 blittable 提高性能。
- **删除**：网络可见性不清晰。

## v55.3.8 -- 2022-Jan-05

- 添加了：[团队兴趣管理](../../interest-management/team.md)和网络团队组件。
- 添加：[设备验证器](../../components/network-authenticators/device-authenticator.md)。
- 新增：自定义兴趣管理[脚本模板](../script-templates.md)。
- 添加：自定义网络转换[脚本模板](../script-templates.md)。
- 添加：将旋转参数添加到` NetworkTransform``CmdTeleport `/`RpcTeleport`。
- 新增：支持更多可空类型。
- 添加：[添加剂水平](../../examples/additive-levels.md)示例。
- 新增：Texture2D 和 Sprite 现在支持[数据类型](../../guides/data-types.md)。
- 固定：快速进入播放模式，现在支持无广告领域重置所有静态。
- 修正：网络转换现在只根据敏感度同步更改，从而使用更少的带宽。
- 修正：KCP 更新到 1.15 版。
- 修正：`ReplacePlayerForConnection`现在正确更新`isLocalPlayer`标志。
- 修正：`OnStopClient`在某些情况下不再调用两次。
- 修正：`NetworkServer`现在在调用兴趣管理更新之前检查`active`。
- 修正：通过兴趣管理重生时，与客户端所有者一起重生的对象不再失去所有权。
- 修正：延迟断开在基本身份验证器现在使用连接挂起断开`HashSet`。
- 修正：`NetworkTransform`现在在向服务器发送更新之前检查就绪的客户端。
- 修正：在`RemovePlayerForConnection`之后`AddPlayerForConnection`使用相同的播放器对象再次正确调用`OnStartLocalPlayer`
- 已更改：`NetworkMatch`已移动到"`InterestManagement/Match`"文件夹。
- 已更改：`SyncVar`的内部代码。 可能需要重置非默认检查器值。
- 改变：坦克的例子更新。
- 更改：聊天示例更新.真的彻底改造了。
- 更改：网络读取器/写入器现在尽可能使用可直接复制的序列化(性能提高 4- 6 倍)。
- **中断**：删除过时`ConfigureServerFrameRate`\
  请改用`ConfigureHeadlessFrameRate`。
- **中断**：删除过时的`PersistNetworkManagerToOfflineScene`。
- **中断**：` NetworkAuthenticator``OnClientAuthenticated `事件不再需要 NetworkConnection 参数。
- **注意**：网络管理器中的 OnClient\* 虚拟方法不再采用`NetworkConnection`参数。
- **问题**：网络会议室管理器客户端覆盖不再具有`NetworkConnection`参数。 在覆盖中使用`NetworkClient.connection`。
