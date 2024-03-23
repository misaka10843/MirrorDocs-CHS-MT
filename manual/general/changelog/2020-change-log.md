# 2020 变更日志

{% hint style="info"%}
镜像使用语义版本控制，此处显示的版本是发布到资产存储的版本，偶尔会在存储提交之间的月中发生重大版本冲突，因此此处不单独显示。
联系我们

## v30.2.2 -- 2020 年 12 月 17 日<a href="#version-3022----2020-dec-17" id="version-3022----2020-dec-17"></a>

- 增加：网络阅读器/作家现在做`ReadBlittable`/`WriteBlittable`更好的性能。
- 添加：KCP 传输更新到版本 1.4。
- 添加：网络身份验证器现在有`OnStopServer`和`OnStopClient`覆盖。
- 新增：网络变换现在适用于压缩旋转。
- 添加：`SyncVars`现在支持使用从`NetworkBehaviour`继承的类型。
- 添加：`SimpleWebTransport`现在可以在传输之外选择 TLS。
- 添加：`MultipleMatches`示例，具有大堂和匹配功能的网络匹配功能。
- 修正：网络 Ping 显示的更好的呈现。
- 修正：中间件传输现在正确侦听内部事件。
- 修正：现在禁止多次调用`StartServer`/`StartClient`。
- 修正：NRE 在`MultipleAdditiveScenes`示例中`PlayerScore`中没有主机玩家时。
- 修正：在`MultipleAdditiveScenes`例子中的玩家碰撞。
- 修正：Weaver 现在可以正确地为其他程序集中的结构生成读取器和写入器，例如`Mathematics`。
- 修正：`NetworkServer.maxConnections`现在是公开的。
- 修正：跳过重置被销毁的对象。
- 修正：`NetworkRoomManager`中`ReadyStatusChanged`现在是公开的。
- 修正：`NetworkTransform`现在正确使用`ArraySegment`命令。
- 修正：增加`OnStopServer`到`NetworkSceneChecker`和`NetworkMatchChecker`。
- 修正：Weaver 方法查找与 Unity 2020.2 冲突。
- 修正：`NetworkReader`中的内存分配漏洞。
- 更改：播放模式现在阻止无效的场景 ID 的，与错误消息。
- 更改：实验网络变换现在使用`Channels.DefaultUnreliable`并对旋转应用压缩。
- 更改：示例中的仅服务器场景对象替换为服务器上实例化的普通对象或删除。
- **中断：**强制`NetworkMessage`在所有情况下使用结构-不再支持类。
- **中断：**删除`ReadPacked`/`WritePacked`函数-使用 Read 和 Write 变量代替。
- **上一篇：**LLAPI 已被删除

## v26.2.2 -- 2020 年 11 月 20 日<a href="#version-2622----2020-nov-20" id="version-2622----2020-nov-20"></a>

- 上一篇：[KCP](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html)Transport
- [Libuv2k Transport](https://mirror-networking.com/docs/Articles/Transports/Libuv2k.html)。
- 添加：四元数和浮点压缩。
- 新增：在 SimpleWebTransport 中配置 handshakeMaxSize 的选项。
- 添加：服务器传送到实验网络转换。
- 新增：Accept 和 Authenticator 基类的方法，以方便使用。
- 修正：Weaver 在验证器和程序集上的失败，只有消息而没有行为。
- 修正：示例不再依赖于顺序 connectionId。
- 修正：消除了由于传输中的预处理器定义而导致的首次导入错误。
- 修正：场景 ID 记录现在是一致的十六进制格式。
- 修正：使用 BuildPipeline.BuildPlayer 时 sceneId 在路径中大小写不正确的问题。
- 修正：触发器未正确应用于 NetworkAnimator 中的主机。
- 修正：确保 ReadHelper。Read 读取 SimpleWebTransport 中的字节数。
- 更改：最低 Unity 版本现在是 2018.4.28 LTS。
- 更改：脚本模板现在可以作为一个单独的包[在这里](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html)。
- 改变：心灵感应传送现在被废弃-使用[KCP 传送](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html)代替。
- **Breaking：**NetworkIdentity.clientAuthorityCallback 现在是一个事件。
- **突破：**删除旧的过时的方法和测试。
- **中断：**传输不再支持发送到 connectionIds 的列表。

## v23.0.3 -- 2020 年 10 月 24 日<a href="#version-2303----2020-oct-24" id="version-2303----2020-oct-24"></a>

- 新增：序列化类时支持 null。
- 新增：支持递归数据类型.
- 新增：支持锯齿数组.
- 新增：镜像和用户回调所使用类型的新通用读写方法。
- 新增：NetworkAnimator 现在同步 animator.speed。
- 新增：NetworkTransform Server Teleport 功能。
- 新增 Websockets 传输：[SimpleWebTransport](https://github.com/MirrorNetworking/SimpleWebTransport)。
- 修正：ClientScene.localplayer 在销毁时被设置为 null。
- 修正：修正 NetworkRoomPlayer 未调用 IndexChanged 钩子的问题。
- 修复：修复加载场景时的 NullReferenceException。
- 修复：修复 IL2CPP 的 NetworkConnectionToClient。
- 修正：仅在无头服务器中运行时，坦克示例中出现 NullReferenceException。
- 更改：直接使用 SyncLists/SyncSet/SyncDictionary，例如`SyncList`，参见[Pull Request 2307](https://github.com/vis2k/Mirror/pull/2307)。
- 更改：OnSerialize 现在在序列化每个组件之前包含组件索引作为字节。 以带宽为代价获得更好的 CPU 性能。
- 更改：现在每个 NetworkIdentity 可以有 256 个 NetworkBehaviours。
- **中断**：将`IMessageBase`重命名为`NetworkMessage`，请参阅[Pull Request 2317](https://github.com/vis2k/Mirror/pull/2317)。
- **删除**：SyncEvents，请参阅[合并请求 2178](https://github.com/vis2k/Mirror/pull/2178)。
- **移除**：移除 Cmd 和 RPC 的手动调用，请参阅[Pull Request 2223](https://github.com/vis2k/Mirror/pull/2223)。
- **删除**：删除消息中的序列化/重新序列化，请参阅[合并请求 2317](https://github.com/vis2k/Mirror/pull/2317)。
- **删除**：删除 SyncLists/SyncSet/SyncDictionary 的自动初始化，请参阅[Pull Request 2330](https://github.com/vis2k/Mirror/pull/2330)。
- **删除**：用[SimpleWebTransport](https://github.com/MirrorNetworking/SimpleWebTransport)替换[Ninja.WebSockets](https://github.com/MirrorNetworking/NinjaWebSocketsTransport)。

## v17.3.0 -- 2020-Sep-04<a href="#version-1730----2020-sep-04" id="version-1730----2020-sep-04"></a>

- 新增：NetworkAnimator 现在同步层权重。
- 添加：列表现在可以在命令/RPC/消息/等中发送。
- 新增：`[服务器]`/`[客户端]`现在可以在 NetworkBehaviour 之外使用。
- 添加：实验[NetworkLerp 刚体](https://mirror-networking.com/docs/Articles/Components/NetworkLerpRigidbody.html)组件，同步位置和速度，并使用 Lerp 应用它们。
- 修正：NetworkAnimator 现在立即触发所有者。
- 修正：`isServer`在调用`NetworkServer.Destroy`后将保持其值不变。
- 修正：`[客户端]`错误信息现在正确记录方法的名称。
- 修正：消息现在可以嵌套在其他消息类型中。
- 修正：`[服务器]`/`[客户端]`现在正确地给出错误时，对抽象方法使用。
- 修正：抽象类现在可以实现 IMessageBase。
- 修正：Weaver 现在正确地为抽象类生成读取器方法时给出错误。
- 更改：如果没有连接，NetworkServer 不再运行更新。
- 更改：NetworkBehaviour.IsDirty 现在是公共的。
- 已更改：SyncEvent 现已过时，将在下一版本中删除。
- **删除**：isHeadless 现在已过时，使用预处理器指令`#if UNITY_SERVER`代替。

## v16.9.0 -- 2020 年 8 月 1 日<a href="#version-1690----2020-aug-01" id="version-1690----2020-aug-01"></a>

- 新增：[NetworkHeadlessLogger](https://mirror-networking.com/docs/Articles/Components/NetworkHeadlessLogger.html)日志处理程序，设置控制台颜色。
- 新增：新镜像列表服务器，请参阅[云文件夹](https://github.com/vis2k/Mirror/tree/master/Assets/Mirror/Cloud).
- 新增：实验[网络刚体](https://mirror-networking.com/docs/Articles/Components/NetworkRigidbody.html)组件，同步速度和其他刚体设置。
- 修正：如果第一个基类没有覆盖，Command/RPC 内部调用的基类方法现在可以工作。
- 修正：NetworkRoomPlayer 现在清理 roomSlots 禁用。
- 修正：回退和多路复用现在禁用他们的传输时，他们被禁用。
- 修正：Websockets 传输 SocketState 现在返回 false，如果套接字是`undefined`的。
- 修正：如果 SyncEvent 在不同的类中，它们现在可以具有相同的名称。
- 修正：现在每个类可以有多个 SyncEvent。
- 修正：消息基类现在被处理，即使它们是在文件的后面声明的。
- 修正：注册一个预制件和相同的插件不再给出一个错误。
- 修正：Weaver 现在为实现 IMessageBase 的类生成 Serialize 方法。
- 更改：NetworkProximityNetwork 现在的性能稍好。
- 更改：ClientRpc 不再需要 Rpc 前缀。
- 更改：命令不再需要 Cmd 前缀。
- 更改：TargetRpc 不再需要 Target 前缀。
- 更改：NetworkManager.networkSceneName 现在是受保护的设置，因为它不应该被直接设置。

## v16.1.1 -- 2020 年 6 月 13 日<a href="#version-1611----2020-jun-13" id="version-1611----2020-jun-13"></a>

- 新增：\[Command]现在有一个用于在客户端没有权限的对象上调用 Commands`ignoreAuthority`选项，并且 Command 方法可以有一个可选`NetworkConnectionToClient sender`参数。
- 新增：\[ClientRpc]现在有一个`excludeOwner`选项，可以防止消息发送到拥有该对象的客户端。
- 新增：命令和 RPC 现在可以在派生类中声明为虚拟和重写。
- 新增：NetworkLogSettings 组件和日志设置窗口。
- 新增：同步列表现在支持`AddRange`、`InsertRange`和`RemoveAll`。
- 添加：网络室管理器现在有一个虚拟`OnRoomServerPlayersNotReady`，从`CheckReadyToBegin`在服务器上触发。
- 新增：网络房间播放器模板现在包括基本网络行为覆盖。
- 添加：网络房间播放器现在有一个虚拟挂钩的索引 SyncVar，覆盖是在模板中。
- 增加：实验网络变换与更多的控制设置和功能.
- 添加：[网络 Ping 显示](https://mirror-networking.com/docs/Articles/Components/NetworkPingDisplay.html)组件
- 修正：网络房间管理器.minPlayers 现在受到保护，因此它可用于派生类。
- 固定：网络室经理不再做多余的球员预置注册`OnStartClient`。
- 修正：网络室播放器`OnClientEnterRoom`现在正确地只在客户端上发射。
- 修正：`RegisterClientMessages`在每次场景更改时都被错误调用。
- 修正：网络行为`SyncMode`和`SyncInterval`在某些情况下未显示在检查器中（回归）。
- 修正：网络管理器现在在服务器停止时更好地清理网络对象。
- 修正：网络管理器不再尝试在`StopClient`中冗余地更改为离线场景。
- 修正：网络连接的`lastMessageTime`没有被正确初始化，导致不正确的空闲超时断开连接。
- 修正：Websockets 传输现在可以在场景更改期间正确暂停和排队消息，否则应该使用 SSL 更稳定
- 改变：坦克的例子得到了改进。
- 更改：网络房间播放器现在使用虚拟 SyncVar 挂钩 ReadyStateChanged。
- 改变：网络距离侦测器现在使用直接距离侦测来检测玩家物体，而不是物理距离。
- **中断：**Websockets 传输现在需要完整的路径到 PFX 证书文件。
- **标签**：Discord Transport

## v13.0.1 -- 2020 年 5 月 6 日<a href="#version-1301----2020-may-06" id="version-1301----2020-may-06"></a>

- 添加：[多个并发添加场景示例](https://mirror-networking.com/docs/Articles/Examples/MultipleAdditiveScenes/index.html)。
- 添加：[网络匹配](https://mirror-networking.com/docs/Articles/Components/NetworkMatchChecker.html)组件。 使用此组件可以在单个游戏服务器中创建多个并发的孤立比赛。
- 新增：[同步列表](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncLists.html)现在有查找和查找所有功能。
- 添加：网络行为现在有`OnStopServer`和`OnStopClient`虚拟方法。
- 新增：Weaver 现在支持其他程序集中类型的自定义 Reader & Writer。
- 新增：网络管理器现在有一个可选的设置，以检查和断开远程连接，已经沉默了指定的时间间隔。
- 增加：网络服务器和网络客户端现在有`ReplaceHandler`方法，以避免在尝试双重注册消息处理程序时出现警告。
- 新增：`NetworkServer.SendToAll`现在有一个可选的 bool，只发送到准备好的客户端，和一个`SendToReady`方法，不需要网络身份。
- 固定：网络动画不再双火灾 SetTrigger / ResetTrigger 主机客户端。
- 修正：Network Animator 不再限于每个对象一个组件。
- 修正：不再在服务器上为玩家对象调用两次摧毁。
- 修正：`RegisterClientMessages`不再在客户端被调用两次。
- 修正：网络行为`SyncMode`和`SyncInterval`在某些情况下没有显示在检查器中。
- 修正：心灵感应传输-`LateUpdate`现在处理的消息数量有限，每滴答，以避免死锁。
- 更改：网络行为：`OnNetworkDestroy`重命名为`OnStopClient`。
- \*\*\*\*RemovePlayerMessage 作为潜在的安全风险已被删除。 请改用`NetworkServer.RemovePlayerForConnection`。
- **网络**行为：`OnRebuildObservers`、`OnCheckObserver`和`OnSetHostVisibility`被移到一个名为`NetworkVisibility`单独类中。

## v11.4.2 - 2020-Apr-03<a href="#version-1142---2020-apr-03" id="version-1142---2020-apr-03"></a>

- 增加：SyncVar 钩子现在可以是虚拟的，并在派生类中被重写。
- 添加：虚拟`OnRoomStopServer`添加到网络室管理器和脚本模板。
- 添加：10K 网络对象基准测试示例。
- 修正：在 IDE 中设置断点命令和 RPC 的工作现在正确。
- 修正：`NetworkServer.SendToObservers`现在向镜像分析器报告正确的通道。
- 修正：网络房间管理器的`roomPlayerPrefab`现在被`protected`，所以它可以在派生类中访问。
- 修正：网络房间播放器检查器和文档更新，以减少混乱。
- 修正：网络身份不再双重调用`NetworkServer.Destroy`。
- 修正：网络动画现在正确排除参数控制的曲线。
- 修正：网络行为现在为 SyncVar 标签使用属性抽屉，以便更好地显示。
- 修正：`NetworkServer.SendToReady`重载不再模糊。
- 修正：网络房间管理器不再错误地破坏游戏玩家对象。 它留在游戏场景中，当场景改变时由 Unity 清理。
- 修复：`StopHost`在网络管理器中正确引发`OnServerDisconnect`，并在关闭服务器之前正确解绕。
- 修正：在网络身份的`OnDestroy`中，`isServer`在服务器上不再是错误的 false。
- 更改：网络管理器`offlineScene`和`onlineScene`现在存储完整路径，因此使用 SceneManager.GetActiveScene（）.path 代替。
- 更改：网络管理器 HUD 现在更恰当地调用`StopHost`/`StopServer`/`StopClient`。
- 更改：网络管理器 HUD 标签不再显示 LAN。 相关文档也进行了清理，以消除镜像是唯一的 LAN 解决方案的误解。
- 更改：删除网络转换压缩，消息处理现在更简单。
- 更改：网络场景再次关闭 Awake，因为`OnEnable`在某些情况下不可靠。
- 变更日期：如果场景不在"构建设置"场景列表中，网络管理器将不再丢失对场景的引用，但移动或重命名场景可能会导致引用丢失。
- **删除：**许多过时的方法和属性。 如果从 UNet 或旧版 Mirror 升级，请先使用[版本 10](https://github.com/vis2k/Mirror/releases/tag/v10.4.7)。 见[弃](https://mirror-networking.com/docs/Articles/General/Deprecations.html)用完整列表。

## v10.4.7 - 2020-Mar-03<a href="#version-1047---2020-mar-03" id="version-1047---2020-mar-03"></a>

- 新增：韦弗现在将阻止播放模式和建设，如果有韦弗错误，并显示在控制台再次。
- 添加：`PooledNetworkReader`和`PooledNetworkWriter`，都是一次性的。
- 添加：`NetworkReader.ReadMessage`.
- 新增：网络发现现在处理无头服务器模式。
- 增加：SyncVar，Cmd 和 RPC 现在支持通过接收端的实例化脚本对象。
- 添加：Discord Transport _在 v16.1.1 中删除_
- 修正：`isClient`现在在 OnDestroy 中为网络对象的客户端返回 true。
- 修正：主机玩家比赛条件准备消息。
- 修正：网络动画和网络变换现在正确地检查客户端的权力，在各自的命令方法。
- 修正：网络室管理器脚本模板有一个虚拟方法，而不是一个覆盖。
- 修正：网络服务器对 NetworkConnectionToClient 的调用。发送现在包括了丢失的 channelId 参数。
- 已修正：Network Server 对 SendToAll、SendToReady 和 SendToObservers 的调用会发送到被检测为本地连接的确切连接，而不是回退到. localConnection。
- 修正：网络服务器。如果服务器没有运行，SpawnObjects 返回 false。
- 修正：改进了网络变换旋转检测。
- 修正：Weaver 生成的代码现在可以为 IL2CPP 正确构建（再次）。
- 更改：网络管理器中的 StartHost 不再是虚拟方法（也不应该是）。 而不是 OnStartHost。
- 更改：网络室管理器的 OnRoomServerSceneLoadedForPlayer 现在包括 NetworkConnection 参数。
- 更改：网络场景切换现在从 OnEnable 而不是 Awake 工作，并使用场景而不是场景名称。
- 更改：重命名为 NetworkWriter。写入 WriteMessage 以保持一致性。

## v9.0.2 - 2020- 2 月-04<a href="#version-902---2020-feb-04" id="version-902---2020-feb-04"></a>

- 新增：Network Animator 现在有一个 ResetTrigger 功能和服务器/客户端权限警告。
- 增加：网络转换现在有 3 个新的浮动敏感性，以安静下来的消息流量从微观变化。
- 添加：网络观察者添加到[脚本模板](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html)--请参阅资源>创建菜单中的新镜像部分。
- 添加：[网络发现](https://mirror-networking.com/docs/Articles/Components/NetworkDiscovery.html)已经重新实现，包括一个例子和脚本模板-感谢所有那些谁作出了贡献！
- 添加：[网络场景插件](https://mirror-networking.com/docs/Articles/Components/NetworkSceneChecker.html)。
- 新增：所有组件的镜像图标.
- 新增：网络行为、网络转换和网络动画的检查头。
- 添加：URI 添加到支持的数据类型。
- 新增：`NetworkReaderPool`已在任何地方取代`new NetworkReader`，显著减少垃圾分配。
- 修正：网络变换和网络动画现在使用 NetworkWriterPool。
- 修正：网络转换和网络动画现在尊重客户端拥有的对象`hasAuthority`。
- 修正：如果时间/距离太大，网络转换将正确传送。
- 修正：当 clientAuthority 为 false 时，Network Animator 现在可以正确同步。
- 修正：客户端拥有的对象现在在客户端断开连接时被销毁（回归）。
- 修正：在 DontDestroyOnLoad（DDOL）中，客户端权限现在为通过场景更改携带的网络对象保留。
- 修正：取消客户端连接后立即启动服务器不再抛出 NRE。
- 修复：IL2CPP 构建在停止服务器时不再触发断言。
- 修正：在钩子被调用之前，同步变量现在被设置为主机播放器。
- 修正：SyncDictionary 现在在调用回调之前正确更新值。
- 固定：停止主机不再尝试改变到离线场景两次。
- 修正：网络室管理器 roomSlots 现在正确管理服务器和客户端.
- 修正：网络室管理器现在正确地支持多个游戏场景，并增加了一个虚拟`OnRoomServerAddPlayer`。
- 修正：添加场景操作不再错误地导致来自客户端的无关 AddPlayer 消息。
- 修正：`NetworkWriterPool`现在在任何地方都使用，而不是`new NetworkWriter`（我们在实现池时错过了一对夫妇）。
- 修复：Unity 2019.3.x`RequestScriptReload`修补程序已从 UnityEditorInternal.InternalEditorUtility 移动到 EditorUtility。
- 修正：自定义类型的数组现在被正确支持。
- 更改：精简了逻辑。
- 更改：`NetworkIdentity.GetSceneIdenity`方法重命名为`GetSceneIdentity`（名称拼写错误）。
- 更改：OnApplicationQuit 虚拟方法添加到 Transport 类，并且不再从 Network Manager 调用`Transport.activeTransport.Shutdown()`。
- **中断：**SyncVar 钩子现在需要**两个**参数，一个用于旧值，一个用于新值，并且现在**在**调用钩子之前设置属性值。

## v6.7.7 - 2020-Jan-01<a href="#version-677---2020-jan-01" id="version-677---2020-jan-01"></a>

- 新增：[脚本模板](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html)--查看资源>创建菜单中新的镜像部分。
- 添加：全文搜索添加到文档。
- 添加：基本聊天示例.
- 添加：一些 YouTube 视频已经创建，并在适当的情况下从各种文档页面链接。
- 新增：传输现在可以支持使用 URI 连接，包括端口。
- 添加：版本. txt 文件现在包含在其中的发布版本。
- 新增：继承自 IMessageBase 的结构现在可以生成序列化器。
- 修正：组件现在出现在 API 参考下的文档中。
- 修正：延迟断开基本身份验证器。
- 修正：多路复用器现在正确处理 OnClientConnect 和 GetMaxMessageSize。
- 修正：网络房间管理器 OnRoomServerGamePlayer 现在包括房间播放器对象作为参数。
- 更改：房间示例现在在离线场景中有网络房间管理器，并在服务器/客户端启动时正确切换到房间场景。
- 更改：网络管理器 startPositionIndex 和 loadingSceneAsync 现在是公共的。
- 更改：SceneMessage 现在有一个可选的 customHandling 标志，这样开发人员就可以处理自己的自定义场景加载，例如使用 Addressables。
- **突破：**网络转换现在默认为服务器权限，并有一个客户端权限复选框。
