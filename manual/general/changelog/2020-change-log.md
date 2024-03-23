# 2020 更新日志

{% hint style="info" %}
Mirror 使用语义化版本控制，这里显示的版本是发布到 Asset Store 的版本，有时会在提交到商店之间发生主要版本升级，因此这里没有单独显示。
{% endhint %}

## v30.2.2 -- 2020 年 12 月 17 日 <a href="#version-3022----2020-dec-17" id="version-3022----2020-dec-17"></a>

- 添加：网络读写器现在执行 `ReadBlittable` / `WriteBlittable` 以获得更好的性能。
- 添加：KCP 传输更新到版本 1.4。
- 添加：网络认证器现在具有 `OnStopServer` 和 `OnStopClient` 重写。
- 添加：网络变换现在对旋转应用压缩。
- 添加：`SyncVars` 现在支持使用继承自 `NetworkBehaviour` 的类型。
- 添加：`SimpleWebTransport` 现在具有在传输外部进行 TLS 的选项。
- 添加：`MultipleMatches` 示例，展示了具有大厅和匹配功能的网络匹配检查器。
- 修复：更好地展示网络延迟显示。
- 修复：中间件传输现在正确监听内部事件。
- 修复：防止多次调用 `StartServer` / `StartClient`。
- 修复：在 `MultipleAdditiveScenes` 示例中，如果 `PlayerScore` 中没有主机玩家，则会出现 NRE。
- 修复：在 `MultipleAdditiveScenes` 示例中的玩家碰撞。
- 修复：Weaver 现在正确为其他程序集中的结构生成读取器和写入器，例如 `Mathematics`。
- 修复：`NetworkServer.maxConnections` 现在是公共的。
- 修复：跳过正在销毁的对象的重置。
- 修复：`NetworkRoomManager` 中的 `ReadyStatusChanged` 现在是公共的。
- 修复：`NetworkTransform` 现在在命令中正确使用 `ArraySegment`。
- 修复：向 `NetworkSceneChecker` 和 `NetworkMatchChecker` 添加了 `OnStopServer`。
- 修复：Weaver 方法查找与 Unity 2020.2 的冲突。
- 修复：`NetworkReader` 中的内存分配漏洞。
- 更改：播放模式现在会阻止无效场景 ID，显示错误消息。
- 更改：实验性网络变换现在使用 `Channels.DefaultUnreliable` 并对旋转应用压缩。
- 更改：示例中的仅服务器场景对象已替换为在服务器上实例化的普通对象或直接消除。
- **重大变更：** 强制 `NetworkMessage` 在所有情况下使用结构体 - 不再支持类。
- **重大变更：** 移除了 `ReadPacked` / `WritePacked` 函数 - 使用读取和写入变体代替。
- **重大变更：** LLAPI 已移除。

## v26.2.2 -- 2020 年 11 月 20 日 <a href="#version-2622----2020-nov-20" id="version-2622----2020-nov-20"></a>

- 新增：[KCP 传输 (Transport)](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html)。
- 新增：[Libuv2k 传输 (Transport)](https://mirror-networking.com/docs/Articles/Transports/Libuv2k.html)。
- 新增：四元数 (Quaternion) 和浮点数 (float) 压缩。
- 新增：在 SimpleWebTransport 中添加配置 handshakeMaxSize 的选项。
- 新增：在实验性 NetworkTransform 中添加 ServerTeleport。
- 新增：在 Authenticator 基类中添加 Accept 和 Reject 方法，以便使用。
- 修复：在 Authenticators 和仅具有消息而没有行为的程序集上的 Weaver 失败。
- 修复：示例不再依赖于顺序连接 ID。
- 修复：由于传输中的预处理器定义，在首次导入时消除错误。
- 修复：现在场景 ID 记录始终以十六进制格式。
- 修复：在使用 BuildPipeline.BuildPlayer 时，路径大小写不正确时的 sceneId。
- 修复：在 NetworkAnimator 中，主机的触发器未正确应用。
- 修复：确保 ReadHelper.Read 在 SimpleWebTransport 中确切读取字节数。
- 更改：最低 Unity 版本现在是 2018.4.28 LTS。
- 更改：脚本模板现在作为单独的包 [这里](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html) 可用。
- 更改：Telepathy 传输 (Transport) 现在已过时 - 使用 [KCP 传输 (Transport)](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html) 代替。
- **重大变更：** NetworkIdentity.clientAuthorityCallback 现在是一个事件。
- **重大变更：** 移除旧的过时方法和测试。
- **重大变更：** 传输 (Transports) 不再支持发送到连接 ID 列表。

## v23.0.3 -- 2020 年 10 月 24 日 <a href="#version-2303----2020-oct-24" id="version-2303----2020-oct-24"></a>

- 新增：在序列化类时支持 null。
- 新增：支持递归数据类型。
- 新增：支持交错数组。
- 新增：用于 mirror 和用户回调的新通用 Read 和 Write 方法。
- 新增：NetworkAnimator 现在同步 animator.speed。
- 新增：NetworkTransform 服务器传送功能。
- 新增：新的 WebSocket 传输 (Transport)：[SimpleWebTransport](https://github.com/MirrorNetworking/SimpleWebTransport)。
- 修复：当 ClientScene.localplayer 被销毁时，现在将其设置为 null。
- 修复：修复 NetworkRoomPlayer 的 IndexChanged 钩子未被调用。
- 修复：加载场景时出现 NullReferenceException 的问题。
- 修复：在仅在无头服务器中运行时，坦克示例中出现 NullReferenceException。
- 更改：直接使用 SyncLists/SyncSet/SyncDictionary，例如 `SyncList`，参见 [Pull Request 2307](https://github.com/vis2k/Mirror/pull/2307)。
- 更改：现在在每个组件序列化之前，OnSerialize 包括组件索引作为字节。在带宽成本下提高 CPU 性能。
- 更改：现在每个 NetworkIdentity 可以有 256 个 NetworkBehaviours。
- **重大变更：** 将 `IMessageBase` 重命名为 `NetworkMessage`，参见 [Pull Request 2317](https://github.com/vis2k/Mirror/pull/2317)。
- **移除：** SyncEvents，参见 [Pull Request 2178](https://github.com/vis2k/Mirror/pull/2178)。
- **移除：** 移除 Cmd 和 RPC 的手动调用，参见 [Pull Request 2223](https://github.com/vis2k/Mirror/pull/2223)。
- **移除：** 移除消息中的 Serialize/Deserialize，参见 [Pull Request 2317](https://github.com/vis2k/Mirror/pull/2317)。
- **移除：** 替换 [Ninja.WebSockets](https://github.com/MirrorNetworking/NinjaWebSocketsTransport) 为 [SimpleWebTransport](https://github.com/MirrorNetworking/SimpleWebTransport)。

## v17.3.0 -- 2020 年 9 月 4 日 <a href="#version-1730----2020-sep-04" id="version-1730----2020-sep-04"></a>

- 添加: NetworkAnimator 现在同步层权重（Layer Weight）。
- 添加: 现在可以在 Command/Rpc/Message 等中发送列表。
- 添加: `[Server]`/`[Client]` 现在可以在 NetworkBehaviour 之外使用。
- 添加: 实验性 [NetworkLerpRigidbody](https://mirror-networking.com/docs/Articles/Components/NetworkLerpRigidbody.html) 组件，同步位置和速度，并使用 Lerp 应用它们。
- 修复: NetworkAnimator 现在立即在所有者上触发触发器。
- 修复: `isServer` 现在在调用 `NetworkServer.Destroy` 后保持其值。
- 修复: `[Client]` 错误消息现在正确记录方法的名称。
- 修复: 现在可以在其他 Message 类型中嵌套消息。
- 修复: `[Server]`/`[Client]` 现在在抽象方法上使用时正确给出错误。
- 修复: 抽象类现在可以实现 IMessageBase。
- 修复: Weaver 现在在为抽象类生成读取器方法时正确给出错误。
- 更改: 如果没有连接，则 NetworkServer 不再运行更新。
- 更改: NetworkBehaviour.IsDirty 现在是公共的。
- 更改: SyncEvent 现在已过时，并将在下一个版本中删除。
- **移除**: isHeadless 现在已过时，请改用预处理指令 `#if UNITY_SERVER`。

## v16.9.0 -- 2020 年 8 月 1 日 <a href="#version-1690----2020-aug-01" id="version-1690----2020-aug-01"></a>

- 添加: [NetworkHeadlessLogger](https://mirror-networking.com/docs/Articles/Components/NetworkHeadlessLogger.html) 日志处理程序，设置控制台颜色。
- 添加: 新的 Mirror List Server，请参阅 [Cloud folder](https://github.com/vis2k/Mirror/tree/master/Assets/Mirror/Cloud)。
- 添加: 实验性 [NetworkRigidbody](https://mirror-networking.com/docs/Articles/Components/NetworkRigidbody.html) 组件，同步速度和其他刚体设置。
- 修复: 在 Command/RPC 中调用的基础方法现在可以正常工作，即使第一个基类没有覆盖。
- 修复: NetworkRoomPlayer 现在在禁用时清理 roomSlots。
- 修复: Fallback 和 Multiplex 现在在禁用时禁用其传输。
- 修复: Websockets 传输 SocketState 现在如果套接字为 `undefined` 则返回 false。
- 修复: SyncEvents 现在可以在不同类中具有相同名称。
- 修复: 现在可以在同一类中拥有多个 SyncEvents。
- 修复: 即使稍后在文件中声明，消息基类现在也会被处理。
- 修复: 注册具有相同 GUID 的预制体不再报错。
- 修复: Weaver 现在为实现 IMessageBase 的类生成序列化方法。
- 更改: NetworkProximityChecker 现在具有稍好的性能。
- 更改: ClientRpc 不再需要 Rpc 前缀。
- 更改: Commands 不再需要 Cmd 前缀。
- 更改: TargetRpc 不再需要 Target 前缀。
- 更改: NetworkManager.networkSceneName 现在受保护设置，不应直接设置。

## v16.1.1 -- 2020 年 6 月 13 日 <a href="#version-1611----2020-jun-13" id="version-1611----2020-jun-13"></a>

- 添加：\[Command] 现在有一个 `ignoreAuthority` 选项，用于在客户端没有权限的情况下调用对象上的命令，并且命令方法可以有一个可选的 `NetworkConnectionToClient sender` 参数。
- 添加：\[ClientRpc] 现在有一个 `excludeOwner` 选项，以防止消息发送到拥有对象的客户端。
- 添加：命令和 Rpc 现在可以声明为虚拟的，并且可以在派生类中重写。
- 添加：NetworkLogSettings 组件和日志设置窗口。
- 添加：SyncLists 现在支持 `AddRange`、`InsertRange` 和 `RemoveAll`。
- 添加：Network Room Manager 现在有一个虚拟的 `OnRoomServerPlayersNotReady`，在服务器上从 `CheckReadyToBegin` 触发。
- 添加：Network Room Player 模板现在包括基础 Network Behaviour 覆盖。
- 添加：Network Room Player 现在具有用于索引 SyncVar 的虚拟钩子，并且覆盖在模板中。
- 添加：具有更多控制设置和功能的实验性 Network Transform。
- 添加：[网络 Ping 显示](https://mirror-networking.com/docs/Articles/Components/NetworkPingDisplay.html) 组件
- 修复：Network Room Manager.minPlayers 现在是受保护的，因此可以供派生类使用。
- 修复：Network Room Manager 不再在 `OnStartClient` 中进行冗余的玩家预制体注册。
- 修复：Network Room Player `OnClientEnterRoom` 现在正确地只在客户端触发。
- 修复：`RegisterClientMessages` 在每次场景更改时被错误地调用。
- 修复：Network Behaviour `SyncMode` 和 `SyncInterval` 在某些情况下未在检查器中显示（回归）。
- 修复：Network Manager 现在在服务器停止时更好地清理网络对象。
- 修复：Network Manager 不再在 `StopClient` 中多余地尝试切换到离线场景。
- 修复：Network Connection 的 `lastMessageTime` 未被正确初始化，导致不正确的空闲超时断开连接。
- 修复：Websockets 传输现在在场景更改期间正确暂停和排队消息，并且在 SSL 方面应该更加稳定
- 更改：对坦克示例进行了改进。
- 更改：Network Room Player 现在使用虚拟 SyncVar 钩子来处理 ReadyStateChanged。
- 更改：Network Proximity Checker 现在直接针对玩家对象进行距离检查，而不是使用 Physics.SphereCastNonAlloc，以获得更好的性能。
- **重大变更：** Websockets 传输现在需要完整的 PFX 证书文件路径。
- **移除：** Discord 传输

## v13.0.1 -- 2020 年 5 月 6 日 <a href="#version-1301----2020-may-06" id="version-1301----2020-may-06"></a>

- 新增: [多并发附加场景示例](https://mirror-networking.com/docs/Articles/Examples/MultipleAdditiveScenes/index.html)。
- 新增: Network Match Checker（网络匹配检查器）组件。使用此组件在单个游戏服务器中创建多个并发隔离的匹配。
- 新增: [SyncLists（同步列表）](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncLists.html) 现在具有 Find 和 FindAll 函数。
- 新增: Network Behaviour（网络行为）现在具有 `OnStopServer` 和 `OnStopClient` 虚拟方法。
- 新增: Weaver 现在支持其他程序集中类型的自定义 Reader 和 Writer。
- 新增: Network Manager（网络管理器）现在具有一个可选设置，用于检查并断开已经在指定时间间隔内保持沉黙的远程连接。
- 新增: Network Server 和 Network Client 现在具有 `ReplaceHandler` 方法，以避免在尝试双重注册消息处理程序时出现警告。
- 新增: `NetworkServer.SendToAll` 现在具有一个可选的布尔值，只发送给准备就绪的客户端，并且有一个 `SendToReady` 方法，不需要 Network Identity。
- 修复: Network Animator（网络动画师）不再在主机客户端上双重触发 SetTrigger / ResetTrigger。
- 修复: Network Animator 不再限制每个对象只能有一个组件。
- 修复: 对于玩家对象，Destroy 不再在服务器上调用两次。
- 修复: `RegisterClientMessages` 不再在客户端上调用两次。
- 修复: Network Behaviour `SyncMode` 和 `SyncInterval` 在某些情况下未显示在检查器中。
- 修复: Telepathy Transport - `LateUpdate` 现在每个时钟周期处理有限数量的消息，以避免死锁。
- 更改: Network Behaviour: `OnNetworkDestroy` 已重命名为 `OnStopClient`。
- **重大变更:** RemovePlayerMessage 已被移除，因为存在潜在的安全风险。请改用 `NetworkServer.RemovePlayerForConnection`。
- **重大变更:** Network Behaviour: `OnRebuildObservers`、`OnCheckObserver` 和 `OnSetHostVisibility` 已移至名为 `NetworkVisibility` 的单独类中。

## v11.4.2 - 2020 年 4 月 3 日 <a href="#version-1142---2020-apr-03" id="version-1142---2020-apr-03"></a>

- 新增: 现在可以将 SyncVar 钩子设置为虚拟方法，并在派生类中进行重写。
- 新增: 在 Network Room Manager 和 Script 模板中新增了虚拟方法 `OnRoomStopServer`。
- 新增: 添加了 10K 网络对象基准示例。
- 修复: 在 IDE 中为 Command 和 Rpc 设置断点现在可以正常工作。
- 修复: `NetworkServer.SendToObservers` 现在在 Mirror Profiler 中报告正确的通道。
- 修复: Network Room Manager 的 `roomPlayerPrefab` 现在是 `protected`，因此可以在派生类中访问。
- 修复: 更新了 Network Room Player 的检查器和文档，使其更加清晰。
- 修复: Network Identity 不再重复调用 `NetworkServer.Destroy`。
- 修复: Network Animator 现在正确排除由曲线控制的参数。
- 修复: Network Behaviour 现在使用属性绘制器来显示 SyncVar 标签，使其显示更好。
- 修复: `NetworkServer.SendToReady` 的重载不再模糊。
- 修复: Network Room Manager 不再错误地销毁游戏玩家对象。当场景更改时，游戏对象将保留在游戏场景中由 Unity 清理。
- 修复: `StopHost` 现在在 Network Manager 中正确引发 `OnServerDisconnect`，并在关闭服务器之前正确解开。
- 修复: `isServer` 在 Network Identity 的 `OnDestroy` 中不再错误地为 false。
- 更改: Network Manager 的 `offlineScene` 和 `onlineScene` 现在存储完整路径，因此请使用 SceneManager.GetActiveScene().path。
- 更改: Network Manager HUD 现在更恰当地调用 `StopHost` / `StopServer` / `StopClient`。
- 更改: Network Manager HUD 标签不再显示 LAN。相关文档也进行了清理，消除了 Mirror 仅为 LAN 解决方案的误解。
- 更改: 移除了 Network Transform 的压缩，现在消息处理更加简单。
- 更改: Network Scene Checker 再次在 Awake 中初始化，因为在某些情况下 `OnEnable` 证明是不可靠的。
- 更改: 如果场景不在 Build Settings 场景列表中，Network Manager 将不再丢失对场景的引用，但移动或重命名场景可能会导致引用丢失。
- **移除:** 许多已过时的方法和属性。如果从 UNet 或旧版 Mirror 升级，请先使用 [version 10](https://github.com/vis2k/Mirror/releases/tag/v10.4.7)。请查看[弃用内容](https://mirror-networking.com/docs/Articles/General/Deprecations.html)获取完整列表。

## v10.4.7 - 2020 年 3 月 3 日 <a href="#version-1047---2020-mar-03" id="version-1047---2020-mar-03"></a>

- 新增：如果存在编织错误，编织器现在会阻止播放模式和构建，并再次在控制台中显示错误。
- 新增：`PooledNetworkReader` 和 `PooledNetworkWriter`，均为可释放的。
- 新增：`NetworkReader.ReadMessage`。
- 新增：网络发现现在处理无头服务器模式。
- 新增：SyncVar、Cmd 和 Rpc 现在通过在接收端使用 CreateInstance 支持 Scriptable Objects（脚本化对象）。
- 新增：Discord 传输 _在 v16.1.1 中已移除_
- 修复：对于网络对象，在 OnDestroy 中 `isClient` 现在会在客户端返回 true。
- 修复：主机玩家 Ready 消息的竞争条件。
- 修复：网络动画器和网络变换现在在各自的 Command 方法中正确检查客户端权限。
- 修复：网络房间管理器脚本模板中的虚拟方法已更改为覆盖方法。
- 修复：网络服务器调用 NetworkConnectionToClient.Send 现在包括了缺失的 channelId 参数。
- 修复：网络服务器调用 SendToAll、SendToReady 和 SendToObservers 如果检测到是本地连接，则发送到确切的连接，而不是回退到 .localConnection。
- 修复：如果服务器未运行，Network Server.SpawnObjects 现在会返回 false。
- 修复：网络变换旋转检测已改进。
- 修复：编织器生成的代码现在再次可以为 IL2CPP 构建。
- 更改：Network Manager 中的 StartHost 现在不再是虚拟方法（也不应该是）。请覆盖 OnStartHost 方法。
- 更改：Network Room Manager 的 OnRoomServerSceneLoadedForPlayer 现在包括 NetworkConnection 参数。
- 更改：网络场景检查器现在从 OnEnable 开始工作，而不是 Awake，并且使用场景而不是场景名称。
- 更改：将 NetworkWriter.Write 重命名为 WriteMessage 以保持一致性。

## v9.0.2 - 2020 年 2 月 4 日 <a href="#version-902---2020-feb-04" id="version-902---2020-feb-04"></a>

- 新增：网络动画器现在具有 ResetTrigger 函数和服务器/客户端权限警告。
- 新增：网络变换现在有 3 个新的浮点数用于对微小变化的消息流量进行抑制。
- 新增：[脚本模板](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html) 中添加了网络观察器 -- 在 Assets > Create 菜单中查看新的 Mirror 部分。
- 新增：[网络发现](https://mirror-networking.com/docs/Articles/Components/NetworkDiscovery.html) 已重新实现，包括示例和脚本模板 -- 感谢所有贡献者！
- 新增：[网络场景检查器组件](https://mirror-networking.com/docs/Articles/Components/NetworkSceneChecker.html)。
- 新增：所有组件添加了 Mirror 图标。
- 新增：网络行为、网络变换和网络动画器添加了检查器标题。
- 新增：URI 添加到支持的数据类型。
- 新增：`NetworkReaderPool` 已在所有位置实现，大大减少了垃圾分配。
- 修复：网络变换和网络动画器现在使用 NetworkWriterPool。
- 修复：网络变换和网络动画器现在尊重客户端拥有对象的 `hasAuthority`。
- 修复：如果时间/距离过大，网络变换现在将正确传送。
- 修复：当客户端断开连接时，现在会销毁客户端拥有的对象（回归）。
- 修复：客户端权限现在在通过场景更改传递的网络对象中得到保留（DontDestroyOnLoad）。
- 修复：取消客户端连接尝试后立即启动服务器不再引发 NRE。
- 修复：在停止服务器时，IL2CPP 构建不再触发断言。
- 修复：现在在调用钩子之前为 Host 玩家设置 SyncVars。
- 修复：SyncDictionary 现在在调用回调之前正确更新值。
- 修复：StopHost 不再尝试两次更改到离线场景。
- 修复：网络房间管理器现在在服务器和客户端上正确管理 roomSlots。
- 修复：网络房间管理器现在正确支持多个游戏场景，并添加了虚拟的 `OnRoomServerAddPlayer`。
- 修复：增量场景操作不再导致客户端发送多余的 AddPlayer 消息。
- 修复：现在在所有位置使用 `NetworkWriterPool` 而不是 `new NetworkWriter`（在实现池时我们错过了一些）。
- 修复：Unity 2019.3.x 的 `RequestScriptReload` 补丁已从 UnityEditorInternal.InternalEditorUtility 移至 EditorUtility。
- 修复：现在正确支持自定义类型的数组。
- 更改：关闭逻辑已简化。
- 更改：`NetworkIdentity.GetSceneIdenity` 方法重命名为 `GetSceneIdentity`（名称拼写错误）。
- 更改：Transport 类中添加了 OnApplicationQuit 虚拟方法，不再从 Network Manager 调用 `Transport.activeTransport.Shutdown()`。
- **重大变更：** SyncVar 钩子现在需要 **两个** 参数，一个用于旧值，一个用于新值，并且在调用钩子之前设置属性值。

## v6.7.7 - 2020 年 1 月 1 日 <a href="#version-677---2020-jan-01" id="version-677---2020-jan-01"></a>

- 添加: [脚本模板](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html) -- 在 Assets > Create 菜单中查看 Mirror 部分。
- 添加: 完整文本搜索已添加到文档中。
- 添加: 基本聊天示例。
- 添加: 已创建一些 YouTube 视频，并在适当的文档页面中进行了链接。
- 添加: 传输现在可以支持使用包括端口在内的 URI 进行连接。
- 添加: version.txt 文件现在包含了发布版本信息。
- 添加: 从 IMessageBase 继承的结构现在会生成序列化器。
- 修复: 组件现在在 API 参考文档下显示。
- 修复: 基本认证器中的延迟断开连接问题。
- 修复: 多路复用器现在正确处理 OnClientConnect 和 GetMaxMessageSize。
- 修复: 网络房间管理器中的 OnRoomServerCreateGamePlayer 现在包括 Room Player Object 作为参数。
- 更改: 房间示例现在在离线场景中具有网络房间管理器，并在启动服务器/客户端时正确切换到房间场景。
- 更改: 网络管理器中的 startPositionIndex 和 loadingSceneAsync 现在是公共的。
- 更改: SceneMessage 现在具有一个可选的 customHandling 标志，使开发人员可以处理自己的自定义场景加载，例如使用 Addressables。
- **重大变更:** 网络变换现在默认具有服务器权限，并具有客户端权限复选框。
