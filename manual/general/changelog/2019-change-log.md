# 2019 更新日志

{% hint style="info" %}
Mirror 使用语义化版本控制，这里显示的版本是发布到 Asset Store 的版本，有时会在提交到商店之间的中月份发生主要版本升级，因此这里没有单独显示。
{% endhint %}

## v6.3.0 - 2019年12月9日 <a href="#version-630---2019-dec-09" id="version-630---2019-dec-09"></a>

* 添加：SyncMode 选择器现在适用于客户端拥有的任何场景对象上的组件，而不仅仅是玩家对象，例如宠物。
* 添加：MessageBase 现在完全支持继承。
* 添加：Room 示例现在有一个 UI 按钮，主机可以将所有客户端返回到 Room 场景，并进行其他改进。
* 修复：ReplacePlayerForConnection 现在对现有场景对象有效，只要另一个玩家没有拥有目标对象。
* 修复：isClient 和 isServer 现在在 Start 和 OnStartClient / OnStartServer 中对网络对象为真。
* 修复：在调用 OnStartClient 和 OnStartLocalPlayer 之前，hasAuthority 现在已设置。
* 更改：connectionToClient 现在用于分配客户端权限。
* 更改：在许多方面，主机玩家的行为更像客户端，并且将更好地显示错误。
* 更改：ReplacePlayerForConnection 现在有一个可选的布尔值，用于保留先前对象的权限（默认 = false）。
* 移除：`NetworkServer.SpawnWithClientAuthority` 已弃用，推荐使用 `NetworkServer.Spawn` 的重载。

## v5.0.2 - 2019年11月3日 <a href="#version-502---2019-nov-03" id="version-502---2019-nov-03"></a>

* 添加：SyncList 和 SyncSet 自定义 Equality Comparer 支持。
* 添加：可以为 Cmd 和 Rpc 中的任何类型实现自定义序列化器。
* 添加：[Fallback Transport](https://mirror-networking.com/docs/Articles/Transports/Fallback.html)。
* 修复：如果没有更改，SyncVar 钩子现在不再在主机中调用。
* 修复：Network Identity 在 RemoveClientAuthority 中不再抛出空引用异常。
* 修复：服务器传输现在在场景更改期间暂停，以防止错误消息。
* 修复：SyncList、SyncDictionary 和 SyncSet 现在使用自定义 IEnumerator 以避免内存分配。
* 修复：在编辑预制时，sceneID 在某些情况下不再重置。
* 更改：PreprocessorDefine 代码移动到 CompilerSymbols 文件夹，以避免缺失符号导致无法将符号添加到项目的悖论。
* 更改：主机玩家不再默认分配权限给所有对象。
* 更改：命令现在不再绕过 Host 玩家的序列化，使调试更容易。
* 更改：本地连接现在维护自己的消息队列。
* 更改：Transport.Available 现在是抽象的。
* 移除：Network Identity：本地玩家权限已被移除，因为不再需要。

## v4.0.7 - 2019年10月3日 <a href="#version-407---2019-oct-03" id="version-407---2019-oct-03"></a>

* 添加: [认证器(Authenticators)](https://mirror-networking.com/docs/Articles/Components/Authenticators/index.html) 支持在连接阶段对客户端进行身份验证。
* 添加: Profiler 事件。这些事件可以被[网络 Profiler(Network Profiler)](https://mirror-networking.com/docs/Articles/Guides/Profiler.html)订阅，以提供可视化信息。
* 添加: 传输现在在 Profiler 事件中包括通道。
* 添加: 传输抽象类现在支持向连接 ID 列表发送消息。
* 修复: SceneMessage 现在具有 sceneOperation 枚举，以便客户端可以正确处理附加场景。
* 修复: NetworkClient 处理程序现在在关闭时被清除。
* 修复: 当客户端连接失败或被拒绝时，离线场景不再重新加载。
* 修复: 当服务器停止时，开始位置索引现在重置为零。
* 修复: 网络房间玩家现在都在 DontDestroyOnLoad 中，因此它们不会在游戏场景中重新生成。
* 修复: 网络房间示例玩家控制器在禁用时恢复主摄像机。
* 修复: 具有不同同步间隔的组件未向客户端发送更新。
* 修复: 在某些情况下，编织器不会编织一些外部程序集。
* 修复: Network Animator 现在为新客户端进行完全同步。
* 修复: Network Behaviour 检查器现在显示私有 SyncVars 的 SyncMode。
* 修复: 从继承类调用父类的 Commands 和 Rpcs 现在按预期工作。
* 修复: 当尝试连接到不存在的主机时，Telepathy 不再挂起。
* 修复: Websockets 传输现在通过 `ServerGetClientAddress` 正确返回客户端端点信息。
* 修复: WebGL 构建现在与 ArraySegment 正常工作。
* 更改: Mirror 现在在发送端不再产生垃圾内存分配。
  * 一些传输可能仍然会有少量垃圾分配。
* 更改: 弃用了 AddPlayerMessage 中的 extraMessage byte\[]，改为更简单的方法来处理[自定义玩家(Custom Players)](https://mirror-networking.com/docs/Articles/Guides/GameObjects/SpawnPlayerCustom.html)。
  * 这是一个破坏性更改: 虚拟方法 OnServerAddPlayer 失去了 AddPlayerMessage 参数。
* 更改: `NetworkIdentity.RemoveAuthorityForConnection` 现在更容易使用: 不再需要提供当前的 "所有者"。
* 更改: 将 `NetworkConnection.playerController` 重命名为 `identity` ... 详细信息请参阅[弃用(Deprecations)](https://mirror-networking.com/docs/Articles/General/Deprecations.html)。
* 更改: 大厅系统重命名为房间(Room)，以更好地对齐名称并为未来的大厅框架让路。{/*examples*/}

## v3.17.4 - 2019年9月4日 <a href="#version-3174---2019-sep-04" id="version-3174---2019-sep-04"></a>

* 添加: 通过扩展方法添加自定义网络读取器和写入器。
* 添加: 在组件上添加网络同步模式选择器，以同步给观察者（默认）或仅同步给所有者。
* 添加: SyncVars 现在支持其他程序集中的结构体和枚举。
* 添加: 支持读取和写入数组段。
* 添加: 网络动画控制器现在支持层。
* 添加: 新的虚拟方法 OnServerChangeScene 到网络管理器。
* 添加: 为智能感知和未来生成的类文档添加 XML 摘要注释。
* 更新示例和文档。
* 修复: 对于预制体变体，SceneID 未设置为 0。
* 修复: 在场景更改时观察者未正确重建。
* 修复: SyncVar 钩子在主机模式下无法更改其他 SyncVars。
* 修复: Telepathy 未在接受的客户端上设置套接字选项。
* 修复: 捕获 IL2CPP 错误。
* 修复: Telepathy 和 Websockets 现在连接 ID 从 1 开始而不是从 2 开始。
* 修复: Websockets 对 SSL 的支持已恢复。
* 修复: 如果自动创建已禁用，网络管理器不再抱怨缺少玩家预制体。
* 修复: 在 Network Transform 中移除了一个垃圾分配。
* 修复: 在某些情况下不必要地调用 NetworkClient.Ready，例如 SceneMessages。
* 更改: 文档已移至网站，并实施了 API 生成的文档。
* 更改: AddPlayerForConnection 处理程序现在是内部的，以保持安全检查完整性。
* 更改: 一堆消息转换为值类型以提高性能。

## v3.11.6 - 2019年7月10日 <a href="#version-3116---2019-jul-10" id="version-3116---2019-jul-10"></a>

* 修复: Telepathy IPv4、IPv6 和 FQDN 现在应该都可以正常工作。
* 修复: 在 UWP 构建中修复了 TelepathyTransport 错误。
* 修复: 现在更好地处理 OnApplicationQuit。
* 修复: 性能和内存分配改进。
* 更改: 现在包含 Telepathy 源码而不是 DLL。

## v3.10.10 - 2019年6月19日 <a href="#version-31010---2019-jun-19" id="version-31010---2019-jun-19"></a>

* 添加: Scene Message 现在支持 SceneMode（Normal / Additive）和 PhysicsMode（2D / 3D）的参数。
* 添加: ClientScene.Send 现在具有可选的 ChannelId 参数。
* 添加: 在示例文件夹中添加 ASMDEF。
* 添加: 支持发送和接收 ArraySegment。
* 添加: 在 SyncLists 中添加 IReadOnlyList。
* 修复: 在某些情况下，网络管理器未正确切换到正确的场景。
* 修复: 在 WebGL 中未找到 ListServer Ping。
* 修复: TelepathyTransport.GetMaxPacketSize 使用新的可配置最大大小。
* 修复: 大幅减少内存分配和垃圾回收。
* 更改: 为了更好的 VR 支持，现在使用 transform.localPosition 和 transform.localRotation。
* 移除: Websockets 发送队列（从未起作用）和 SSL（暂时因为它不起作用）。

## v3.6.7 -- 2019年4月28日 <a href="#version-367----2019-apr-28" id="version-367----2019-apr-28"></a>

* 更改: NetworkReader 现在使用 ArraySegment 来最小化分配。

## v3.6.6 -- 2019年4月24日 <a href="#version-366----2019-apr-24" id="version-366----2019-apr-24"></a>

* 修复: 撤销了两个内部重构提交，导致意外后果。

## v3.6.5 -- 2019年4月23日 <a href="#version-365----2019-apr-23" id="version-365----2019-apr-23"></a>

* 修复: 适配 Unity 2019.1。
* 修复: 错误地指示预制件缺少场景 ID 的错误。
* 修复: 在不分配的情况下，OnDeserializeSafely 现在可以正常工作。
* 修复: Weaver 不写入符号文件，导致 Visual Studio 中断点无法工作。
* 修复: Network Identity SceneID 生成现在使用 RNG 加密服务提供程序。
* 修复: Additive 示例中的场景光照。
* 修复: 在没有 NetworkStartPosition 的情况下，实例化时现在会尊重 Player Prefab 的变换细节。
* 移除: 从 Unity 包生成中移除 Tests 文件夹（无终端用户价值）。

## v3.5.9 -- 2019年4月12日 <a href="#version-359----2019-apr-12" id="version-359----2019-apr-12"></a>

* 修复: Network Manager 循环模式现在使用 NetworkStartPosition 对象，并使用层次结构兄弟顺序。
* 修复: `Start()` 中的 IsLocalPlayer 现在通过 OwnerMessage 和 SpawnPrefabMessage 可靠准确。
* 修复: Weaver 和 Cecil 中的堆栈溢出问题。
* 修复: 传递给 weaver 的程序集路径已更正。
* 修复: 在 SyncVars、Commands 和 Rpcs 中减少了枚举带宽。
* 修复: 为已删除的代码文件添加占位符文件，以便更新效果更好。
* 更改: Network Manager 的 `isHeadless` 现在是一个静态属性，从 `IsHeadless()` 更改而来。

## v3.5.3 -- 2019年4月10日 <a href="#version-353----2019-apr-10" id="version-353----2019-apr-10"></a>

* 修复: Network Manager 和其他组件的重写中的异常错误被错误地消除。
* 修复: 大厅系统有时不会生成并将游戏玩家预制件交换到游戏场景中。
* 修复: 在主机模式下不再设置 Application.targetFrameRate。
* 更改: Telepathy: 将 MaxMessageSize 拆分，以允许为客户端和服务器设置不同的值。

## v3.4.9 -- 2019年4月6日 <a href="#version-349----2019-apr-6" id="version-349----2019-apr-6"></a>

* 添加: 语义化版本控制 (这就是我们从1.6跳到3.4.9的原因).
* 添加: [SyncDictionary](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncDictionary.html) (同步字典).
* 添加: [SyncHashSet](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncHashSet.html) (同步哈希集).
* 添加: [SyncSortedSet](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncSortedSet.html) (同步排序集).
* 添加: SyncList和SyncDictionary现在支持所有IList和IDictionary派生类型，分别.
* 添加: [SyncVars](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncVars.html) (同步变量) 文档.
* 添加: [SyncEvents](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncEvent.html) (同步事件) 文档.
* 添加: Network Transform现在有NoRotation选项.
* 添加: 生成负载现在包括缩放(scale)、位置和旋转.
* 添加: 通用的`IMessageBase`以允许结构化消息类型.
* 添加: Weaver现在支持Vector2Int和Vector3Int.
* 添加: 列表服务器示例.
* 添加: 叠加场景示例.
* 修复: SyncLists现在对基本类型和结构体正常工作.
* 修复: SyncVar Hooks现在在调用钩子之后将更新本地属性值.
  * 您不再需要在钩子方法中手动更新本地属性的代码行.
* 修复: 主机不应在传输上调用Disconnect.
* 修复: NetworkAnimimator现在支持多达64个动画参数.
* 修复: Network Manager `StartServer`不再假设场景零是默认场景...现在使用`GetActiveScene`.
* 修复: `NetworkServer.Shutdown`现在将`netId`重置为零.
* 修复: 当客户端加入并且`OnRebuildObservers` / `OnCheckObserver`被覆盖时，观察者现在被正确重建.
* 修复: NetworkProximityChecker: 在极少情况下，玩家可能会被排除在观察者重建之外.
* 修复: NetworkLobbyPlayer `OnClientReady`现在正常工作.
* 修复: 当仅服务器时，NetworkLobbyPlayer的移除按钮现在对P1可见.
* 修复: NetworkLobbyManager的`pendingPlayers`和`lobbySlots`列表现在对继承者是公开的.
* 修复: 离线场景切换现在通过`StopClient()`正常工作.
* 修复: 更新了Pong示例.
* 修复: 源Weaver正在删除PDB文件，导致断点和调试无法工作.
* 更改: TargetRpc的NetworkConnection参数现在是可选的...调用客户端的NetworkConnection是默认值.
* 更改: 将移动示例替换为坦克示例.
* 更改: NetworkClient函数现在全部是静态的，因此单例已被移除. 直接使用NetworkClient.
* 更改: SyncList现在直接支持结构体，使SyncListSTRUCT过时.
* 移除: SyncListSTRUCT - 使用SyncList代替.
* 移除: NetworkClient.ShutdownAll已过时 -- 使用NetworkClient.Shutdown代替.

## v1.6 -- 2019年3月14日 <a href="#version-16----2019-mar-14" id="version-16----2019-mar-14"></a>

* 修复：Websockets 传输移至 Mirror.Websocket 命名空间。
* 修复：网络动画控制器（Network Animator）带宽滥用问题。
* 修复：网络动画控制器（Network Animator）浮点同步 bug。
* 修复：网络对象的持久场景ID。
* 更改：[传输](https://mirror-networking.com/docs/Articles/Transports/index.html)的文档。
* 更改：Weaver 现在是完整源码...终于！
* 更改：ClientScene.AddPlayer 的第二个参数现在是 `byte[] extraData`，而不是 `MessageBase extraMessage`。
  * 请参考这里的代码示例 [here](https://mirror-networking.com/docs/Articles/Guides/GameObjects/SpawnPlayerCustom.html) 来查看如何更新您的代码。
* 更改：网络管理器（Network Manager）-- 无头自动启动移至 `Start()`，而不是 `Awake()`。
* 更改：移除所有消息的消息ID - 详细信息请参阅[网络消息](https://mirror-networking.com/docs/Articles/Guides/Communications/NetworkMessages.html)。
  * 消息ID现在根据消息名称自动生成。
  * 以前您会调用 Send(MyMessage.MsgId, message)，现在您调用 Send(message)。
* 移除：Groove 传输的文档 - 请改用 Websockets 传输。

## v1.5 -- 2019年3月1日 <a href="#version-15----2019-mar-01" id="version-15----2019-mar-01"></a>

* 添加：**迁移工具**（Migration Tool），可（大部分）自动转换从 UNet 迁移过来的内容。
* 添加：完全支持 WebSockets 和 WebSocketsSecure 以替代 UNet LLAPI。
* 添加：传输多路复用器（Transport Multiplexer）- 允许同时使用多个传输。
* 添加：NetworkLobbyManager 和 NetworkLobbyPlayer，附带示例游戏。
* 添加：网络管理器（Network Manager）中可配置的服务器 Tick 率。
* 添加：新的虚拟函数 OnClientChangeScene 在执行 SceneManager.LoadSceneAsync 之前触发。
* 添加：Weaver 的单元测试。
* 修复：消除了许多地方的垃圾分配（还有更多工作要做，我们知道）。
* 修复：NetworkProximityChecker 现在使用 `OverlapSphereNonAlloc` 和 `OverlapCircleNonAlloc`。
* 修复：当客户端加入时，SyncVar 钩子未触发的问题。
* 修复：网络管理器（Network Manager）不再假设自己在场景（0）中。
* 修复：网络动画控制器（Network Animator）不再限制为 6 个变量。
* 修复：TelepathyTransport 在禁用时仍传递消息的问题。
* 更改：最低 Unity 版本：**2018.3.6**
* 移除：SceneAttribute.cs（合并到CustomAttributes.cs）。
* 移除：`NetworkClient.allClients`（改用 `NetworkClient.singleton`）。
* 移除：`NetworkServer.hostId` 和 `NetworkConnection.hostId`（LLAPI 的遗留物）。
* 移除：`NetworkConnection.isConnected`（网络连接始终连接）。
* 移除：`Transport.GetConnectionInfo`（改用 `ServerGetClientAddress`）。

## v1.4 -- 2019年2月1日 <a href="#version-14----2019-feb-01" id="version-14----2019-feb-01"></a>

* 添加: 组件的 HelpURL 属性。
* 添加: 无头构建的自动 targetFramerate。
* 添加: ByteMessage 到 Messages 类。
* 修复: 连接状态可以被正确取消。
* 修复: 网络变换插值应用于客户端自己的对象。
* 修复: 对象生成时具有正确的旋转。
* 修复: SceneId 分配。
* 修复: 更改的 syncInterval 没有保存...现在已保存。
* 修复: Additive 场景加载。
* 更改: **Mirror 现在是完整源代码** -- 不再使用 DLL。
* 更改: **传输现在是组件** -- TCP, UDP, WebGL, Steam。
* 更改: Transport 类现在分发 Unity 事件。
* 更改: `NetworkServer.SendToClientOfPlayer` 现在使用 `NetworkIdentity`。
* 更改: `NetworkServer.SendToObservers` 现在使用 `NetworkIdentity` 参数。
* 更改: `NetworkServer.SendToReady` 现在使用 `NetworkIdentity`。
* 更改: `NetworkServer.DestroyPlayerForConnection` 现在使用 `NetworkIdentity.spawned`。
* 更改: `NetworkConnection.Dispose` 现在使用 `NetworkIdentity.spawned`。
* 更改: `NetworkReader.ReadTransform` 现在使用 `NetworkIdentity.spawned`。
* 更改: 网络变换重新实现 -- 物理移除，代码简化。
* 移除: `NetworkClient.hostPort` (端口由传输层处理)。
* 移除: `NetworkServer.FindLocalObject` (使用 `NetworkIdentity.spawned[netId]` 替代)。
* 移除: `ClientScene.FindLocalObject` (使用 `NetworkIdentity.spawned[netId]` 替代)。
