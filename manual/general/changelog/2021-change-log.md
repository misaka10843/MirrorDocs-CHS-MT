# 2021 更新日志

{% hint style="info" %}
Mirror 使用语义化版本控制，这里显示的版本是发布到 Asset Store 的版本，有时会在商店提交之间的中月发生主要版本升级，因此这里没有单独显示。
{% endhint %}

## v53.0.0 -- 2021 年 10 月 20 日

- 添加: `ChangeOwnerMessage`。`NetworkIdentity.AssignClientAuthority` 和 `RemoveClientAuthority` 现在发送此消息，而不是 `SpawnMessage`，因此转换数据不再重置为服务器值，移动对象不再会出现快速回弹。
- 添加: `SyncDictionary` 现在可以从现有列表中声明和初始化。
- 添加: 可空颜色添加到 Mirror 处理的数据类型中。
- 添加: `ILPostProcessor` 现在用于 Unity 版本 2020.3 及更高版本。
- 修复: 停止发现不再导致崩溃。
- 修复: 附加场景示例中放错了场景对象。
- 修复: `NetworkManager.ServerChangeScene` 现在如果场景更改已在进行中，则会中止。
- 修复: 当取消生成场景对象时，客户端上的 `hasAuthority` 现在会正确清除。
- 修复: 匹配兴趣管理现在正确处理没有 `NetworkMatch` 组件的网络对象，并且在某些情况下不再抛出 Key not found 异常。
- 修复: `NetworkManager.StopClient` 在某些情况下不再会触发两次。
- 修复: `NetworkMatch` 缺少属性。
- 更改: `NetworkLoop` 静态类现在是公共的。
- 更改: `NetworkConnection.observing` 现在是公共的。

  更改: `NetworkTransformV2` 默认值已更新。`interpolateScale` 现在也默认为 `false`。

- 更改: `NetworkTransformV2` 的 `isClientAuthority` 现在是 `protected`，而不是 `private`。
- 更改: 使用 `NetworkTransformV2` 的示例现在使用默认值。
- 更改: 更新了 `NetworkManager` 脚本模板。
- 更改: 坦克示例现在有生命条。
- 更改: `componentIndex` 现在在 `CmdMessage` 和 `RpcMessage` 结构中是 `byte` 类型。
- 更改: `SyncObject` 现在是一个类，而不是一个接口。
- **已弃用**: `SyncObject.Flush` - 使用 `ClearChanges` 代替。
- **已弃用**: `NetworkBehaviour.getSyncVarHookGuard` - 使用 `GetSyncVarHookGuard` 代替。
- **已弃用**: `NetworkBehaviour.setSyncVarHookGuard` - 使用 `SetSyncVarHookGuard` 代替。
- **已弃用**: `NetworkBehaviour.SetDirtyBit` - 使用 `SetSyncVarDirtyBit` 代替。
- **已弃用**: `NetworkServer.SendToReady` - 使用 `SendToReadyObservers` 代替。
- **已移除**: 各种已过时的方法，至少已有 3 个月。
- **已移除**: 回退传输 -- 不再包含或支持。

## v46.0.4 - 2021 年 9 月 3 日

- 新增：坦克示例现在使用 `GUIConsole` 进行更轻松的调试。
- 修复：在缺少处理程序的情况下，`UnpackAndInvoke` 现在会抛出错误并断开连接。
- 修复：`NetworkServer`/`NetworkClient` 现在可以检测到不断增长的批次。
- 修复：Weaver 现在在 Unity 2020+ 中使用 `ILPostProcessing`。
- 修复：在 `NetworkTransformV2` 中的移动延迟问题。
- 修复：`NetworkTransformBase.OnDrawGizmos` 现在只在播放模式下运行。
- 修复：在 Network Manager 模板中添加了缺失的 `using`。
- 修复：一些 `OnGUI` 调用现在被包裹在 `#if UNITY_EDITOR || DEVELOPMENT_BUILD` 中。
- 更改：`NetworkLoop` 中的 `NetworkEarlyUpdate`/`NetworkLateUpdate` 现在通过 Action 事件公开。
- 更改：多个匹配示例现在使用匹配兴趣管理。
- 更改：多个附加场景示例现在使用场景兴趣管理。
- **已弃用**：`NetworkIdentity.spawned` 已拆分为 `NetworkServer.spawned` 和 `NetworkClient.spawned`。
- **已移除**：Network Observer 模板。

## v44.0.2 -- 2021 年 8 月 8 日

- 新增：场景兴趣管理。将其放在 Network Manager 上，并移除所有 Network Scene Checker 组件。
- 新增：匹配兴趣管理。将其放在 Network Manager 上，并用新的 Network Match 组件替换 Network Match Checker。
- 新增：兴趣管理现在具有 `OnSpawned` / `OnDestroyed` 事件。
- 新增：带有快照插值的 Network Transform V2。
- 新增：网络传输异常事件，传输可以引发这些事件到 Network Manager 的 `OnServerError` 和 `OnClientError` 覆盖。传输仍然需要实现其部分。
- 新增：`ZigZag VarInt` 再次添加到压缩模块。
- 修复：Network Discovery HUD 现在在停止服务器/主机/客户端时调用 `StopDiscovery`。
- 修复：`NetworkTime.lastPingTime` 现在被正确重置。
- 修复：KCP 更新到版本 1.12。
- 修复：`NetworkBehaviour` 的脏检查现在使用双倍时间。
- 修复：`NetworkTime.UpdateClient` 使用双倍精度时间以确保准确性跨天/周。
- 修复：`NetworkAnimator` 使用双倍精度时间以确保准确性跨天/周。
- 修复：对于某些值，四元数压缩会产生错误的结果。
- 修复：向 `NetworkAnimator.CmdSetAnimatorSpeed` 添加了缺失的 `[Command]` 属性。
- 修复：在清理场景标识时现在调用 `DestroyObject`。发送未生成消息并正确调用 `OnStopClient`/`OnStopServer`。
- 更改：距离兴趣管理现在具有自定义范围组件，取代 Network Proximity Checker。
- 更改：每场景兴趣管理取代 Network Scene Checker。
- 更改：Network Match 组件与 Network Match Checker 分离。
- 更改：匹配兴趣管理取代 Network Match Checker。
- 更改：`NetworkManager.isNetworkActive` 现在是只读布尔值。
- 更改：`NetworkManager.ConfigureServerFrameRate` 重命名为 `ConfigureHeadlessFrameRate`。

## v42.2.12 -- 2021 年 7 月 12 日

- 新增：基于时间戳的批处理。现在批处理始终开启且是双向的。
- 新增：`NetworkServer` / `NetworkClient` 的 `OnConnected` / `OnDisconnected` 事件现在是公共的，以便自定义网络管理器可以连接到它们。
- 修复：`NetworkServer` / `NetworkClient` 的 `Shutdown` 现在正确清除 `OnConnected` / `OnDisconnected` 事件处理程序。
- 修复：现在使用批处理加载场景正常工作。
- 修复：KCP 1.11 显著减少了垃圾分配。
- 修复：回退传输现在不会传播 Early/Late Update。
- 修复：对于主机客户端，`OnClientDisconnect` 没有被调用。
- 修复：兴趣管理 V2 现在允许覆盖 `SetHostVisibility`。
- 修复：使用帧计数而不是单精度时间来处理超过一天的序列化精度。
- 修复：在 `networkBehaviours` 数组为空时添加空值检查和错误日志记录。
- 修复：当父对象处于非活动状态时，强制调用 `NetworkIdentity.Awake`。
- 修复：对于主机客户端，`OnClientChangeScene` 没有触发。
- 修复：在 `UnpackAndInvoke` 中避免空引用异常。
- 修复：在 `WriteUri` 和 `ReadUri` 中处理空值。
- 修复：当 `customHandling` 为 true（增量场景）时，不应调用 `FinishLoadScene`。
- 修复：防止 assetId 被设置为空字符串。
- 修复：`clientAuthority` 现在在 `NetworkRigidbody` / `NetworkRigidbody2D` 中是公共的。
- 更改：在场景加载期间，传输现在不再暂停（禁用）。消息处理器现在正确处理这一点，因此传输可以简化。

## v40.0.9 -- 2021 年 6 月 8 日

- 新增：在基本认证器中添加了 `ServerAuthFailed` 布尔值。
- 新增：Discovery HUD 现在有一个停止按钮。
- 修复：KCP 更新到版本 1.10。
- 修复：Telepathy 更新到版本 1.8。
- 修复：`NetworkIdentity` 默认脚本执行顺序设置为 -1。
- 修复：Discovery 现在在 `OnDisable` / `OnDestroy` 中正确关闭。
- 修复：当客户端连接到游戏服务器时，Discovery 现在停止发现。
- 修复：`NetworkTransform` 同步比例和插值。
- 修复：防止玩家对象被添加到网络管理器的可生成预制体列表中。
- 修复：简单的 Web Sockets 传输 SSL 握手。
- 修复：如果 `NetworkManager` 不处理，`NetworkServer.OnDisconnect` 现在调用 `DestroyPlayerForConnection`。
- 修复：`NetworkClient.Disconnect` 现在检查空连接和不重复调用。
- 修复：服务器启动时现在重置 `NetworkTime`。
- 修复：基本示例中 `PlayerUI` 文本已更正。
- 修复：Telepathy 中的超时问题已解决。
- 修复：其他客户端断开连接后观察者现在正确清除。
- 更改：从网络认证器的客户端消息中移除了 `NetworkConnection` 参数。
- 更改：`MessagePacking GetId<T>` 返回值更改为 `ushort`。
- 更改：`Transport.ServerDisconnect` 的返回值现在是 void 而不是 bool。
- 移除：网络管理器的空闲超时。
- 移除：云 API 和示例。
- **已过时**：回退传输将在以后的版本中移除。

## v35.1.0 -- 2021 年 3 月 29 日

- 新增：网络发现现在有一个布尔值用于禁用自动主动发现。
- 新增：`PersistNetworkManagerToOfflineScene` 到网络管理器...查看工具提示。
- 新增：延迟模拟传输，可以添加到网络管理器，并将正常传输链接到它以模拟网络延迟。
- 新增：同步模式工具提示。
- 新增：无头客户端现在设置 `targetFramerate` 就像无头服务器一样。
- 新增：`NetworkTransform` 现在具有以下功能：
  - 压缩旋转选项
  - 同步缩放选项
  - 插值缩放选项
- 修复：已解决找不到写入器/找不到读取器错误。
  - **注意：** 您可能需要删除 `Library/ScriptAssemblies` 文件夹和 IL2CPP 缓存
- 修复：`NetworkIdentity.isLocalPlayer` 仅设置，但从不重置。修复了一个 bug，在 `OnDestroy` 中 `isLocalPlayer` 为 false，因此某些组件无法在 `OnDestroy` 中依赖它。
- 修复：`NetworkIdentity.Reset` 现在在 `isLocalPlayer` / `ClearLocalPlayer` 检查之后重置 `isLocalPlayer`。
- 修复：`NetworkIdentity.OnDestroy` 现在仅在我们仍然是本地玩家时清除 `NetworkClient.localPlayer`。
- 修复：客户端关闭不再通过用户代码调用 `StopClient` 两次运行。
- 修复：在 Unity 2020 版本中未显示 `SyncVar` 字段标签（Unity bug）。
- 更改：网络管理器 `OnServerAddPlayer` 现在将连接 ID 添加到对象名称中（仅在服务器端，不同步到客户端）。
- 更改：`NetworkTime` 中的几个字段已重命名。之前的字段名称仍然存在且**已过时**，将在以后的版本中删除。
- 更改：大部分 `ClientScene` 现在**已过时**，请改用 `NetworkClient` 中的相同或类似属性。`ClientScene` 将在以后的版本中完全弃用和移除。
- 更改：包含 `NetworkConnection` 的 `NetworkClient.RegisterHandler` 重载现在**已过时**，将在以后的版本中移除。处理程序可以直接使用 `NetworkClient.connection`。
- 更改：通道常量名称已缩短。原始常量仍然存在且**已过时**，将在以后的版本中删除。
- **重大变更**：最低支持的 Unity 版本现在是 2019.4.x LTS。
- **重大变更**：移除了 `ClientScene.onLocalPlayerChanged`。
- **重大变更**：编译器符号裁剪到 Mirror 17 及以后的版本。

## v32.1.4 -- 2021 年 3 月 8 日

- 添加: `NetworkIdentity` 现在具有 `isServer` (服务器端) / `isClientOnly` (仅客户端)。
- 添加: 全局兴趣管理。这可能最终会取代场景检查器、匹配检查器和所有者检查器组件。
- 添加: GUI 控制台从 uMMORPG 移植过来（按 F12 显示/隐藏）。
- 添加: 当离线场景被定义时，网络管理器现在会在 `StopServer` (停止服务器) / `StopClient` (停止客户端) 中将自身从 DDOL 中移除，以避免单例冲突。
- 添加: 消息批处理。在网络管理器中启用。
- 添加: 自定义玩家循环:
  - `NetworkEarlyUpdate` (在任何 Update/FixedUpdate 之前)
  - `NetworkLateUpdate` (在任何 Update/FixedUpdate/LateUpdate 之后) 循环
- 添加: 将 KCP Tick 拆分为 `TickIncoming` (接收) / `TickOutgoing` (发送) 以利用新的 NetworkEarly/LateUpdate 函数...最小化延迟。
- 添加: 推送 > 拉取广播 第一部分。
- 修复: Telepathy 1.7 接收超时默认禁用，将 `MirrorTransport` 超时从 5 秒增加到 30 秒以覆盖场景更改。
- 修复: `NetworkServer.SpawnObserversForConnection` 重构以支持所有可见性情况（`ForceShown` (强制显示) / `ForceHidden` (强制隐藏) / `Default` (默认)）和所有系统（传统 / 新 / 默认）。
- 修复: `NetworkManager.StopServer/StopClient`: 避免在 `OnApplicationQuit` (应用程序退出) 或测试中调用时出现 `NullReferenceException` (空引用异常)。
- 修复: `NetworkServer.Update`: 将非活动连接检查移至主连接 `foreach` 循环中。消除了一个 `foreach`-connections 循环。
- 修复: `NetworkServer.Update`: 将单独的连接更新循环移至主循环中。现在只有一个连接循环。
- 更改: `NetworkManagerHUD.showGUI` 现在已过时，并将在以后的版本中移除。
- 更改: `SendToClientOfPlayer` 现在已过时，并将在以后的版本中移除。
- **重大变更**: 移除了 `LogFactory` 和日志资产功能。
- **重大变更**: 命令 `ignoreAuthority` 参数重命名为 `requiresAuthority` (需要权限)，默认为 true。
- **重大变更**: `ClientRpc` 中的 `excludeOwner` 参数重命名为 `includeOwner`，默认为 true。
- **重大变更**: `ClientScene.spawnableObjects` 不再是公共的。

## v30.5.3 -- 2021 年 2 月 15 日 <a href="#version-3053----2021-feb-15" id="version-3053----2021-feb-15"></a>

- 修复: 修复了批处理消息大于 MTU 的 bug。
- 修复: Telepathy 1.6 修复了数据竞争并提高了稳定性。
- 修复: KCP 1.8 修复了发送/接收空消息未定义行为的 bug，并修复了在任天堂 Switch 上的 IPv6。
- 更改: 网络管理器默认最大连接数现在是 100。
- **Breaking**: `NetworkServer` / `NetworkClient` 不再使用 Connect/Disconnect 消息。连接/断开事件由传输层引发。
- **Breaking**: `OnServerError` / `OnClientError` 已移除，因为没有传输层实现它们。
- **Breaking** 移除了 `NetworkConnection.InvokeHandler`，因为不再需要。

## v30.5.0 -- 2021 年 2 月 5 日 <a href="#version-3050----2021-feb-05" id="version-3050----2021-feb-05"></a>

- 添加: [KCP 传输](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html) 更新到版本 1.7。
- 添加: 服务器消息的批处理支持 - 查看网络管理器以启用。
- 添加: [网络所有者检查器](https://mirror-networking.com/docs/Articles/Components/NetworkOwnerChecker.html)。
- 添加: [脚本模板](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html) 已重新包含在包中 -- 在 Assets > Create 菜单中的新 Mirror 部分查看。
- 添加: KCP 传输添加了不可靠通道。
- 修复: 对 KCP 传输进行了性能改进。
- 修复: 带有 UI 的示例现在再次适用于 Unity 2018.4
- 修复: `NetworkServer.OnConnected` 现在允许 `connectionId` < 0。
- 修复: 修正了 `NetworkRoomPlayer` 的 `ReadyStateChanged` `oldReadyState` 参数名称。
- 更改: 如果传输层提供不可靠通道，则 Ping/Pong 消息现在使用不可靠通道。
- **Breaking** 移除了 Read / Write Blittable。
