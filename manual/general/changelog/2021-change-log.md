# 2021 变更日志

{% hint style="info"%}
镜像使用语义版本控制，此处显示的版本是发布到资产存储的版本，偶尔会在存储提交之间的月中发生重大版本冲突，因此此处不单独显示。
联系我们

## v53.0.0 -- 2021 年 10 月 20 日

- 标签：`ChangeOwnerMessage` `NetworkIdentity.AssignClientAuthority`和`RemoveClientAuthority`现在发送此消息而不是`SpawnMessage`，因此转换数据不再重置为服务器值，因此不再对移动对象进行快速恢复。
- 新增：`SyncDictionary`现在可以从现有 List 声明和初始化。
- 新增：可空颜色添加到数据类型镜像句柄.
- 添加：`ILPostProcessor`现在用于 Unity 2020.3 及更高版本。
- 修正：停止发现不再导致崩溃。
- 修正：添加场景的例子有错位的场景对象。
- 修正：`NetworkManager.ServerChangeScene`现在中止，如果场景变化已经在进行中。
- 修正：取消生成场景对象时，客户端上`hasAuthority`现在正确清除。
- 修正：Match Interest Management 现在可以正确处理没有`NetworkMatch`组件的网络对象，并且在某些情况下不再抛出 Key not found 异常。
- 修正：`NetworkManager.StopClient`在某些情况下不再触发两次。
- 修正：`NetworkMatch`缺少属性。
- 更改：`NetworkLoop`静态类现在是公共的。
- 更改：`NetworkConnection.observing`现在是公开的。

  Changed: `NetworkTransformV2` default values updated. `interpolateScale`现在也默认为`false`。

- 更改：` NetworkTransformV2``isClientAuthority `现在`protected`而不是`private`。
- 更改：使用`NetworkTransformV2`的示例现在使用默认值。
- 更改：更新`NetworkManager`脚本模板。
- 改变：坦克的例子现在有健康条。
- 更改：`componentIndex`现在是`CmdMessage`和`RpcMessage`结构中的`byte`。
- 更改：`SyncObject`现在是一个类而不是接口。
- **Objective**：`SyncObject.Flush`-使用`ClearChanges`代替。
- **Objective**：`NetworkBehaviour.getSyncVarHookGuard`-改用`GetSyncVarHookGuard`。
- **Objective**：`NetworkBehaviour.setSyncVarHookGuard`-改用`SetSyncVarHookGuard`。
- **Objective**：`NetworkBehaviour.SetDirtyBit`-改用`SetSyncVarDirtyBit`。
- \*\*\*\*`NetworkServer.SendToReady`使用`SendToReadyObservers`代替。
- **删除**：至少 3 个月前的各种过时方法。
- **已删除**：回退传输--不再包含或支持。

## v46.0.4 - 2021-Sep-03

- 新增：坦克的例子现在有`GUIConsole`更容易调试。
- 修正：`UnpackAndInvoke`现在抛出错误，并在缺少处理程序的情况下断开连接。
- 修正：`NetworkServer`/`NetworkClient`现在检测不断增长的批次。
- 修复：Weaver 现在在 Unity 2020+中使用`ILPostProcessing`。
- 修正：`NetworkTransformV2`中的移动延迟。
- 修正：`NetworkTransformBase.OnDrawGizmos`现在只能在播放模式下运行。
- 修正：网络管理器模板中增加了缺少`using`。
- 修正：一些`OnGUI`调用现在被包装在`#if UNITY_EDITOR中|| DEVELOPMENT_BUILD`。
- 更改：`NetworkLoop`中`NetworkEarlyUpdate`/`NetworkLateUpdate`现在通过 Action 事件公开。
- 更改：多个匹配示例现在使用匹配兴趣管理。
- 更改：多个添加场景示例现在使用场景兴趣管理。
- **Objective**：`NetworkIdentity.spawned`已被拆分为`NetworkServer.spawned`和`NetworkClient.spawned`。
- **删除**：网络观察者模板。

## v44.0.2 -- 2021 年 8 月 8 日

- 新增：场景兴趣管理。 将其放在网络管理器上，并删除所有网络场景中的组件。
- 添加：匹配兴趣管理。 将其放在网络管理器上，并将网络匹配组件替换为新的网络匹配组件。
- 新增：利息管理现在有`OnSpawned`/`OnDestroyed`事件。
- 新增：网络转换 V2 与快照插值。
- 添加：网络传输异常事件，传输可以引发网络管理器的`OnServerError`和`OnClientError`覆盖。 交通运输仍然需要履行自己的职责。
- 添加：`ZigZag VarInt`添加回压缩模块。
- 修正：网络发现 HUD 现在在停止服务器/主机/客户端时调用`StopDiscovery`。
- 修正：`NetworkTime.lastPingTime`现在正确重置。
- 修正：KCP 更新到 1.12 版。
- 修正：`NetworkBehaviour`脏检查使用双倍时间。
- 修正：`NetworkTime.UpdateClient`使用双精度时间的准确性超过天/周.
- 修正：`NetworkAnimator`使用双精度时间来提高几天/几周的精度
- 修正：四元数压缩会对某些值产生错误的结果。
- 修正：在`NetworkAnimator.CmdSetAnimatorSpeed`中添加了缺少`[的命令]`属性。
- 修正：在清理场景身份时也会调用`DestroyObject`。 发送 unspawn 消息并正确调用`OnStopClient`/`OnStopServer`。
- 修复：销毁时清除`hasAuthority`并调用`OnStopAuthority`。
- 更改：距离兴趣管理现在有自定义范围组件，取代网络接近度。
- 更改：按场景兴趣管理取代网络场景管理。
- 已更改：网络匹配组件与网络匹配插件分离。
- 更改：匹配兴趣管理取代网络匹配管理。
- 更改：`NetworkManager.isNetworkActive`现在是只读 bool。
- 更改：`NetworkManager.ConfigureServerFrameRate`重命名为`ConfigureHeadlessFrameRate`。

## v42.2.12 -- 2021- 2012 年 7 月

- 添加：基于时间戳的时间戳。 批处理现在始终处于打开状态，并且是双向的。
- 新增：`NetworkServer`/` NetworkClient``OnConnected `/`OnDisconnected`事件现在是公开的，因此自定义网络管理器可以挂钩到它们。
- 修正：`NetworkServer`/`NetworkClient`现在可以正确清除`OnConnected`/`OnDisconnected`事件处理程序。`Shutdown`
- 修正：场景加载与安装工程现在。
- 修正：KCP 1.11 显著减少了垃圾分配。
- 修复：回退传输没有传播早期/晚期更新。
- 修复：`OnClientDisconnect`没有被主机客户端调用。
- 修正：利息管理 V2 现在允许`SetHostVisibility`。
- 修正：使用帧计数而不是单精度时间的序列化精度。
- 修正：当`networkBehaviours`数组为空时，增加了空检查和错误记录。
- 固定：强制调用`NetworkIdentity.Awake`，父母是不活动的。
- 修复：`OnClientChangeScene`未为主机客户端触发。
- 修正：避免 NRE 在`UnpackAndInvoke`。
- 修复：`WriteUri`和`ReadUri`中的错误处理。
- 修正：当`customHandling`为 true 时，不应调用`FinishLoadScene`（添加场景）。
- 修正：防止 assetId 被设置为空字符串。
- 修正：`clientAuthority`现在在`NetworkRigidbody`/`NetworkRigidbody2D`中公开。
- 更改：在场景加载期间不再暂停（禁用）传输。 消息处理器现在可以正确处理这个问题，因此可以简化传输。

## v40.0.9 -- 2021 年 6 月 8 日

- 添加：`ServerAuthFailed`bool 到基本身份验证器。
- 发现 HUD 现在有一个停止按钮。
- 修正：KCP 更新到 1.10 版本。
- 修正：心灵感应更新到 1.8 版。
- 修正：`NetworkIdentity`默认脚本执行顺序设置为-1。
- 修正：发现现在正确关闭在`OnDisable`/`OnDestroy`。
- 修正：发现现在停止发现时，客户端连接到游戏服务器。
- 修正：`NetworkTransform`同步缩放和插值。
- 修正：玩家对象现在不能被添加到网络管理器的可生成预设列表中
- 固定：简单的 Web 套接字传输 SSL 握手。
- 修正：`NetworkServer.OnDisconnect`现在调用`DestroyPlayerForConnection`，如果`NetworkManager`不处理它。
- 修正：`NetworkClient.Disconnect`现在检查空连接，并且它没有被调用两次。
- 修正：服务器启动时网络时间重置。`NetworkTime`
- 修正：基本示例`PlayerUI`文本更正。
- 修正：心灵感应中的超时修正。
- 修正：在其他客户端断开连接后，观察者现在可以正确清除。
- 已更改：从网络身份验证器的客户端消息中删除`NetworkConnection`参数。
- 更改：`MessagePacking GetId<T>`返回值更改为`ushort`。
- 更改：`Transport.ServerDisconnect`返回现在为 void 而不是 bool。
- 已删除：网络管理器的空闲故障。
- 删除：云 API 和示例。
- **注意**：Fallback Transport 将在以后的版本中删除。

## v35.1.0 -- 2021 年 3 月 29 日

- 新增：网络发现现在有一个 bool 来禁用自动主动发现。
- 添加：`PersistNetworkManagerToOfflineScene`到网络管理器.参见工具提示。
- 添加：延迟模拟传输，可以添加到网络管理器，并有正常的传输链接到它来模拟网络延迟。
- 新增：同步模式工具提示。
- 新增：无头客户端现在像无头服务器一样设置`targetFramerate`
- 新增：`NetworkTransform`现在具有以下功能：
  - 压缩旋转选项
  - 同步比例选项
  - 插值比例选项
- 修正：未找到写入器/未找到读取器错误已解决。
  - **注意：**您可能需要删除`Library/ScriptAssemblies`文件夹和 IL 2CPP 缓存
- 修正：`NetworkIdentity.isLocalPlayer`只被设置，但不会被重置。 修复了一个在` OnDestroy``isLocalPlayer `为 false 的错误，因此某些组件在`OnDestroy`中无法依赖它。
- 修正：`NetworkIdentity.Reset`现在重置`isLocalPlayer`_after_ the`isLocalPlayer`/`ClearLocalPlayer`检查。
- 修正：如果我们仍然是本地玩家`NetworkIdentity.OnDestroy`现在只清除`NetworkClient.localPlayer`。
- 修正：客户端停止不再通过用户代码调用`StopClient`运行两次。
- 已修复：`SyncVar`字段标签未在 Unity 2020 版本中显示（Unity 错误）。
- 更改：网络管理器`OnServerAddPlayer`现在将连接 ID 添加到对象名称（仅在服务器上，不同步到客户端）。
- 更改：`NetworkTime`中的几个字段已重命名。 以前的字段名称仍然存在，并**已过时**，将在以后的版本中删除。
- 更改：大多数`ClientScene`现在已经**过时**，在`NetworkClient`中使用相同或相似的属性。 `ClientScene`将在以后的版本中完全弃用并删除。
- 更改：包含` NetworkConnection``NetworkClient.RegisterHandler `重载现在已**过时**，并将在以后的版本中删除。 处理程序可以直接使用`NetworkClient.connection`。
- 更改：缩短了通道常量名称。 原始常量仍然存在，并且**已经过时**，并且将在以后的版本中删除。
- \*\*\*\*最低支持的 Unity 版本现在是 2019.4.x LTS。
- **中断**：`ClientScene.onLocalPlayerChanged`已删除。
- **突破**：镜子 17 和更高版本中剔除了更多的符号。

## v32.1.4 -- 2021-Mar-08

- 新增：`NetworkIdentity`现在有`isServer`/`isClientOnly`。
- 补充：全球利益管理。 这可能最终导致替换场景渲染、匹配渲染和所有者渲染组件。
- 添加：GUI 控制台从 uMMORPG 移植（F12 显示/隐藏）。
- 添加：网络管理器现在在定义离线场景时将自己从`StopServer`/`StopClient`中的 DDOL 中取出，以避免单例冲突。
- 添加：消息。 在网络管理器中启用。
- 添加：自定义播放器循环：
  - `NetworkEarlyUpdate`（在任何更新/固定更新之前）
  - `NetworkLateUpdate`（在任何 Update/FixedUpdate/LateUpdate 之后）循环
- 增加：KCP Tick 分为`TickIncoming`/`TickOutgoing`，以利用新的 NetworkEarly/LateUpdate 功能...最小化延迟。
- 添加：推>拉广播部分 1.
- 修正：心灵感应 1.7 接收超时默认禁用，增加`MirrorTransport`超时从 5 秒到 30 秒，以涵盖场景的变化。
- 修复：`NetworkServer.SpawnObserversForConnection`重构以支持所有可见性情况（`ForceShown`/`ForceHidden`/`Default`）和所有系统（legacy / new / default）。
- 修复：`NetworkManager.StopServer/StopClient`：在`OnApplicationQuit`或测试中调用时避免`NullReferenceException`。
- 修正：`NetworkServer.Update`：非活动连接检查移动到主连接`foreach`循环. 消除`foreach`-connections 循环。
- 修正：`NetworkServer.Update`：单独的连接更新循环移动到主循环中. 现在只有一个连接循环。
- 已更改：`NetworkManagerHUD.showGUI`现已过时，将在以后的版本中删除。
- 更改：`SendToClientOfPlayer`现在已过时，将在以后的版本中删除。
- **中断**：`LogFactory`和日志资产功能被删除。
- **中断**：命令`ignoreAuthority`参数重命名为`requiresAuthority`，默认值为 true。
- **中断**：` ClientRpc``excludeOwner `参数重命名为`includeOwner`，默认值为 true。
- **打破**：`ClientScene.spawnableObjects`不再是公共的。

## v30.5.3 -- 2021 年 2 月 15 日<a href="#version-3053----2021-feb-15" id="version-3053----2021-feb-15"></a>

- 修正：修正了一个错误，在邮件大于 MTU。
- 修正：心灵感应 1.6 修正了数据竞赛并提高了稳定性。
- 修复：KCP 1.8 修复了空消息发送/接收未定义的行为，并修复了 Nintendo Switch 上的 IPv6。
- 更改：网络管理器默认最大连接数现在为 100。
- **中断**：`NetworkServer`/`NetworkClient`不再使用连接/断开消息。 连接/断开连接事件由传输引发。
- **中断**：`OnServerError`/`OnClientError`删除，因为没有传输实现它们。
- **断开已**删除的`NetworkConnection.InvokeHandler`，因为不再需要。

## v30.5.0 -- 2021 年 2 月 5 日<a href="#version-3050----2021-feb-05" id="version-3050----2021-feb-05"></a>

- 添加：[KCP 传输](https://mirror-networking.com/docs/Articles/Transports/KCPTransport.html)更新到版本 1.7。
- 新增：批处理支持服务器消息-请参阅网络管理器启用。
- 添加：[网络所有者](https://mirror-networking.com/docs/Articles/Components/NetworkOwnerChecker.html)。
- 新增：[脚本模板](https://mirror-networking.com/docs/Articles/General/ScriptTemplates.html)回到包中--查看资源>创建菜单中新的镜像部分。
- 增加：不可靠的通道添加到 KCP 传输。
- 修正：KCP 传输的性能改进。
- 修复：带有 UI 的示例现在可以再次与 Unity 2018.4 一起使用
- 修正：`NetworkServer.OnConnected`现在允许`connectionId`< 0。
- 修正：修正` NetworkRoomPlayer``ReadyStateChanged``oldReadyState `参数名称。
- 更改：Ping/Pong 消息现在使用不可靠的通道，如果从传输可用。
- 已删除**中断**读/写 Blittable。
