# 2019 变更日志

{% hint style="info"%}
镜像使用语义版本控制，此处显示的版本是发布到资产存储的版本，偶尔会在存储提交之间的月中发生重大版本冲突，因此此处不单独显示。
联系我们

## v6.3.0 - 2019- 12 -09<a href="#version-630---2019-dec-09" id="version-630---2019-dec-09"></a>

- 新增：SyncMode 选择器现在适用于客户端拥有的任何场景对象上的组件，以及玩家对象，例如宠物。
- 添加：MessageBase 现在完全支持继承。
- 添加：房间示例现在有 UI 按钮，用于主机将所有客户端返回到房间场景和其他改进。
- 修正：ReplacePlayerForConnection 现在适用于现有的场景对象，只要另一个玩家不拥有目标对象。
- 修正：isClient 和 isServer 现在分别适用于 Start 和 OnStartClient / OnStartServer 中的联网对象。
- 修正：在调用 OnStartClient 和 OnStartLocalPlayer 之前设置 hasAuthority。
- 更改：connectionToClient 现在用于分配客户端权限。
- 改变：在许多方面，主机玩家的行为更像一个客户端，将更好地揭示错误。
- 更改：ReplacePlayerForConnection 现在有一个可选的 bool 来保留前一个对象的权限(默认值= false)。
- 已删除：不建议使用`NetworkServer.SpawnWithClientAuthority`，以支持`NetworkServer.Spawn`的重载。

## V5.0.2 - 2019- 11 -03<a href="#version-502---2019-nov-03" id="version-502---2019-nov-03"></a>

- 新增：SyncList 和 SyncSet 自定义相等比较器支持。
- 添加：自定义序列化器可以为 Cmd 和 Rpc 中的任何类型实现。
- 补充：[Fallback Transport](https://mirror-networking.com/docs/Articles/Transports/Fallback.html)。
- 修正：如果没有变化，SyncVar 钩子不再在主机中调用。
- 修正：网络身份不再在 RemoveClientAuthority 中引发空引用异常。
- 修正：服务器传输现在暂停在场景变化，以防止错误的消息。
- 修正：SyncList、SyncDictionary 和 SyncSet 现在使用自定义 IEnumerator 来避免内存分配。
- 修正：在某些情况下，编辑预制件时，sceneID 不再重置。
- 变更日期：PreprocessorDefine 代码移到了 PreprocesserSymbols 文件夹，以避免丢失符号的悖论，从而阻止将符号添加到项目中。
- 改变：宿主玩家不再获得默认分配给所有对象的权限。
- 更改：命令不再绕过主机播放器的序列化，使调试更容易。
- 更改：本地连接现在维护自己的消息队列。
- 更改：运输.可用现在是抽象的.
- 删除：网络身份：本地玩家权限已被删除，因为不再需要。

## 2019 年 10 月 3 日星期四<a href="#version-407---2019-oct-03" id="version-407---2019-oct-03"></a>

- 新增：[验证器](https://mirror-networking.com/docs/Articles/Components/Authenticators/index.html)支持在连接阶段验证客户端。
- 添加：Profiler 事件。 这些事件可由[Network Profiler](https://mirror-networking.com/docs/Articles/Guides/Profiler.html)订阅以提供可视信息。
- 增加：传输现在包括通道在分析事件。
- 增加：传输抽象类现在支持发送消息到连接 id 列表.
- 修正：SceneMessage 现在有 sceneOperation 枚举，因此客户端可以正确处理添加场景。
- 修正：NetworkClient 处理程序现在在网络中被清除。
- 修复：当客户端连接失败或被拒绝时，离线场景不再重新加载。
- 修正：当服务器停止时，开始位置索引现在重置为零。
- 修正：网络室玩家现在都在 DontDestroyOnLoad，所以他们不会在游戏场景中重生。
- 修正：网络房间的例子球员控制器恢复主摄像头禁用。
- 修正：具有不同同步间隔的组件无法向客户端发送更新。
- 修正：在某些情况下，编织者不会编织一些外部程序集。
- 修正：网络动画师现在为新客户端做完全同步。
- 修正：网络行为检查器现在显示私有 SyncVars 的 SyncMode。
- 修正：从继承的类调用父类的命令和 RPC，它应该工作。
- 修正：心灵感应不再挂起当试图连接到一个不存在的主机。
- 修复：Websockets Transport 现在通过`ServerGetClientAddress`正确返回客户端端点信息。
- 修正：WebGL 构建现在与 ArraySegment 一起工作。
- 更改：镜像现在在发送端没有垃圾内存分配。
  - 一些传输可能仍然有一点垃圾分配。
- 更改：弃用 AddPlayerMessage extraMessage 字节\[]，以便更轻松地使用[自定义播放器](https://mirror-networking.com/docs/Articles/Guides/GameObjects/SpawnPlayerCustom.html)。
  - 这是一个突破性的更改：虚拟方法 OnServerAddPlayer 丢失了 AddPlayerMessage 参数。
- 更改：`NetworkIdentity.RemoveAuthorityForConnection`现在更容易用途：不再需要提供当前"所有者"。
- 更改：将`NetworkConnection.playerController`重命名为`identity`... 详细信息[请](https://mirror-networking.com/docs/Articles/General/Deprecations.html)参见弃用。
- 更改：大厅系统更名为房间，以更好地调整名称，它是什么，并为未来的大厅框架。

## v3.17.4 - 2019-九月-04<a href="#version-3174---2019-sep-04" id="version-3174---2019-sep-04"></a>

- 新增：自定义网络阅读器和作家通过扩展方法.
- 新增：组件上的网络同步模式选择器，用于同步到观察者(默认)或仅所有者。
- 新增：SyncVars 现在支持其他程序集中的结构和枚举。
- 增加：支持读取和写入数组段.
- 新增：Network Animator 现在支持图层。
- 添加：新的虚拟方法 OnServerChangeScene 到网络管理器。
- 添加：智能感知和未来生成的类文档的 XML 摘要注释。
- 更新示例和文档。
- 修正：SceneID 没有为预制变体设置为 0。
- 修正：观察者在场景变化时没有正确重建。
- 修正：SyncVar 挂钩无法在主机模式下更改其他 SyncVar。
- 修正：心灵感应没有在接受的客户端上设置套接字选项。
- 修正：捕捉 IL2CPP 错误。
- 修正：心灵感应和 Websockets 现在开始连接 ID 的 1，而不是 2。
- 修复：Websockets 对 SSL 的支持恢复。
- 固定：网络管理器不再抱怨失踪球员预置，如果自动创建被禁用。
- 修正：删除了网络转换中的垃圾分配。
- 修正：NetworkClient.Ready 在某些情况下被不必要地调用，例如 SceneMessages。
- 变更：文档移至网站，并实施 API 生成的文档。
- 更改：AddPlayerForConnection 处理程序现在是内部的，以保持安全检查完好无损。
- Changed：一堆消息转换为值类型以提高性能。

## v3.11.6 - 2019- 2010 年 7 月<a href="#version-3116---2019-jul-10" id="version-3116---2019-jul-10"></a>

- 修正：心灵感应 IPv4，IPv6 和 IPv6 现在都可以工作了。
- 修正：UWP 构建中的 TelepathyTransport 错误。
- 修正：OnApplicationQuit 现在处理得更好。
- 修正：性能和内存分配改进。
- 更改：心灵感应源现在包括而不是一个 DLL。

## v3.10.10 - 2019-Jun-19<a href="#version-31010---2019-jun-19" id="version-31010---2019-jun-19"></a>

- 增加：场景消息现在支持场景模式(正常/添加)和物理模式(2D / 3D)的参数。
- 新增：ClientScene.Send 现在有一个可选的参数"ClientId"。
- 添加：ASMDEF 到 Examples 文件夹。
- 新增：支持发送和接收 ArraySegment
- 新增：SyncLists 中的 IReadOnlyList。
- 修正：网络管理器在某些情况下无法切换到正确的场景。
- 修正：WebGL 中找不到 ListServer Ping。
- 修正：TelepathyTransport.GetMaxPacketSize 使用新的可配置的最大大小。
- 修正：内存分配和垃圾回收的显著减少。
- 更改：使用 transform.localPosition 和 transform.localRotation 以获得更好的 VR 支持。
- 删除：Websockets 发送队列(它们从未工作过)和 SSL(暂时因为它不工作)。

## v3.6.7 -- 2019 年 4 月 28 日<a href="#version-367----2019-apr-28" id="version-367----2019-apr-28"></a>

- 更改：NetworkReader 现在使用 ArraySegment 来最小化分配。

## v3.6.6 -- 2019 年 4 月 24 日<a href="#version-366----2019-apr-24" id="version-366----2019-apr-24"></a>

- 修复：恢复了两个产生意外后果的内部重构提交。

## v3.6.5 -- 2019 年 4 月 23 日<a href="#version-365----2019-apr-23" id="version-365----2019-apr-23"></a>

- 修复：Unity 2019.1 兼容性。
- 修正：错误的错误，指出预制件丢失场景 ID 的。
- 修复：OncurializeSafely 现在可以在没有分配的情况下工作。
- 修正：Weaver 不写符号文件，阻止断点在 Visual Studio 中工作。
- 修正：网络身份场景 ID 生成现在使用 RNG 加密服务提供程序。
- 修正：场景照明在加法的例子。
- 固定：球员预置转换的细节现在尊重时，在没有 NetworkStartPosition 实例化。
- 删除：Unity 软件包生成中的 Tests 文件夹(无最终用户值)。

## v3.5.9 -- 2019 年 4 月 12 日<a href="#version-359----2019-apr-12" id="version-359----2019-apr-12"></a>

- 修正：使用 NetworkStartPosition 对象的网络管理器循环模式现在使用层次兄弟顺序。
- 修正：IsLocalPlayer 现在通过结合 OwnerMessage 和 SpawnPrefabMessage 在`Start()`中可靠地准确。
- 修正：Weaver 和 Cecil 的堆栈溢出问题。
- 修正：修正了传递给编织器的装配路径。
- 修复：SyncVars、Commands 和 Rpcs 中的枚举带宽减少。
- 修正：为删除的代码文件添加占位符文件，以便更新效果更好。
- 更改：网络管理器`isHeadless`现在是一个静态属性，从`IsHeadless()`更改。

## v3.5.3 -- 2019 年 4 月 10 日<a href="#version-353----2019-apr-10" id="version-353----2019-apr-10"></a>

- 修正：网络管理器和其他组件的覆盖错误静音。
- 修正：大厅系统有时不会产生和交换游戏玩家预制到游戏场景。
- 修正：主机模式下不再设置 Application.targetFrameRate。
- 更改：心灵感应：拆分 MaxMessageSize，允许为客户端和服务器设置不同的值。

## v3.4.9 -- 2019 年 4 月 6 日<a href="#version-349----2019-apr-6" id="version-349----2019-apr-6"></a>

- 添加：语义版本控制(这就是为什么我们从 1.6 跳到 3.4.9)。
- 标签：SyncDictionary[](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncDictionary.html)
- 标签：[SyncHashSet](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncHashSet.html)
- 添加：[SyncSortedSet](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncSortedSet.html)。
- 新增：SyncList 和 SyncDictionary 分别支持所有 IList 和 IDictionary 派生类型。
- 添加：[SyncVars](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncVars.html)的文档。
- 添加：[SyncEvents](https://mirror-networking.com/docs/Articles/Guides/Sync/SyncEvent.html)的文档。
- 添加：NoRotation to Network Transform。
- 增加：比例现在包括在产卵负载随着位置和旋转。
- 添加：通用`IMessageBase`允许结构消息类型。
- Weaver 现在支持 Vector2Int 和 Vector3Int。
- 添加：列表服务器示例。
- 添加：添加场景示例。
- 修正：同步列表现在可以正确地工作于原语和结构。
- 修正：SyncVar 钩子现在会在钩子被调用后更新本地属性值。
  - 您不再需要在钩子方法中添加一行代码来手动更新本地属性。
- 修正：主机不应该在传输时调用 Disconnect。
- 修正：NetworkAnimimator 现在支持多达 64 个动画参数。
- 修复：网络管理器`StartServer`不再假设场景零是默认场景...现在使用`GetActiveScene`。
- 修正：`NetworkServer.Shutdown`现在将`netId`重置为零。
- 修正：当客户端加入和`OnRebuildObservers`/`OnCheckObserver`被覆盖时，观察者现在可以正确重建。
- 修正：网络接近度：在极少数情况下，玩家可以从观察者重建中排除。
- 修正：NetworkLobbyPlayer`OnClientReady`现在工作。
- 固定：NetworkLobbyPlayer 删除按钮不显示 P1 时，服务器只。
- 修正：NetworkLobbyManager`pendingPlayers`和`lobbySlots`列表现在对继承者公开。
- 修复：离线场景切换现在可以通过`StopClient()`进行。
- 修正：乒乓球的例子更新。
- 修正：Source Weaver 删除 PDB 文件，阻止断点和调试工作。
- 更改：TargetRpc NetworkConnection 参数现在是可选的.调用客户端的 NetworkConnection 为默认值。
- 更改：移动示例替换为坦克示例。
- 更改：NetworkClient 函数现在都是静态的，所以单例消失了。 直接使用 NetworkClient。
- 更改：SyncList 现在直接支持结构体，使 SyncList 中的 CT 过时。
- 已删除：SyncList 删除 CT-使用 SyncList 代替。
- 删除：NetworkClient.ShutdownAll 已过时--请改用 NetworkClient. ShutdownAll。

## v1.6 -- 2019 年 3 月 14 日<a href="#version-16----2019-mar-14" id="version-16----2019-mar-14"></a>

- 修正：Websockets 传输移动到镜像.Websocket 命名空间。
- 修正：网络动画带宽滥用。
- 修正：网络动画浮动同步错误。
- 固定：网络对象的持久场景 ID。
- 变更：[运输](https://mirror-networking.com/docs/Articles/Transports/index.html)文件。
- 修改：Weaver 现在是完整的源...终于！
- 更改：ClientScene.AddPlayer 第二个参数现在是`字节 extraData`而不是`MessageBase extraMessage`。
  - 请参考[此处](https://mirror-networking.com/docs/Articles/Guides/GameObjects/SpawnPlayerCustom.html)的代码示例以了解如何更新代码。
- 更改：网络管理器-无头自动启动从`Awake()`)移动到`Start()`。
- 更改：删除所有消息的消息 ID-请参阅[网络消息](https://mirror-networking.com/docs/Articles/Guides/Communications/NetworkMessages.html)了解详情。
  - 消息 ID 现在根据消息名称自动生成。
  - 以前您会调用 Send(MyMessage.MsgId，message)，现在您调用 Send(message)。
- 已删除：Groove 传输的文档-请改用 Websockets 传输。

## v1.5 -- 2019 年 3 月 1 日<a href="#version-15----2019-mar-01" id="version-15----2019-mar-01"></a>

- 添加：**迁移工具**(大部分)自动从 UNet 转换。
- 新增：完全支持 WebSockets 和 WebSocketsSecure 以取代 UNet LLAPI。
- 增加：传输多路复用器-允许使用多个并发传输。
- 添加：NetworkLobbyManager 和 NetworkLobbyPlayer 与示例游戏。
- 新增：可配置的服务器在网络管理器滴答率.
- 新增：新的虚拟 OnClientChangeScene 会在 SceneManager.LoadSceneAsync 执行之前触发。
- 添加：Weaver 的单元测试。
- 修正：垃圾分配从很多东西中删除(我们知道还有更多的工作要做)。
- 修正：NetworkProximityNode 现在使用`OverlapSphereNonAlloc`和`OverlapCircleNonAlloc`。
- 修正：SyncVar 钩子在客户端加入时不触发。
- 修正：网络管理器不再假设它在构建设置中的场景(0)上。
- 修正：网络动画不再限于 6 个变量。
- 修正：TelepathyTransport 在禁用时传递消息。
- 已更改：最低 Unity 版本：**2018.3.6**
- 删除：SceneAttribute.cs(合并到 CustomAttributes.cs)。
- 删除：`NetworkClient.allClients`(使用`NetworkClient.singleton`代替)。
- 删除：`NetworkServer.hostId`和`NetworkConnection.hostId`(LLAPI 的保留)。
- 删除：`NetworkConnection.isConnected`(网络连接始终处于连接状态)。
- 删除：`Transport.GetConnectionInfo`(改用`ServerGetClientAddress`)。

## v1.4 -- 2019 年 2 月 1 日<a href="#version-14----2019-feb-01" id="version-14----2019-feb-01"></a>

- 添加：组件的 HelpURL 属性。
- 新增：无头版本的自动目标帧率。
- 添加：ByteMessage 到 Messages 类。
- 修正：连接状态可以正常取消。
- 修正：网络变换插值应用于客户端自己的对象。
- 固定：对象以正确的旋转产生。
- 固定：场景 ID 分配。
- 已修复：未保存更改的 syncInterval...现在是了
- 修正：加性场景加载。
- 更改：**镜像现在是完整的源代码**-没有更多的 DLL 的。
- 更改：**传输现在是组件**- TCP，UDP，WebGL，Steam。
- 更改：传输类现在分派 Unity 事件。
- 更改：`NetworkServer.SendToClientOfPlayer`现在使用`NetworkIdentity`。
- 已更改：`NetworkServer.SendToObservers`现在使用`NetworkIdentity`参数。
- 更改：`NetworkServer.SendToReady`现在使用`NetworkIdentity`。
- 已更改：`NetworkServer.DestroyPlayerForConnection`现在使用`NetworkIdentity.spawned`。
- 已更改：`NetworkConnection.Dispose`使用`NetworkIdentity.spawned`spawned。
- 更改：`NetworkReader.ReadTransform`现在使用`NetworkIdentity.spawned`。
- 更改：网络转换重新实现-物理删除，代码简化。
- 删除：`NetworkClient.hostPort`(端口在传输级别处理)。
- 删除：`NetworkServer.FindLocalObject`(改用`NetworkIdentity.spawned[netId]`)。
- 已删除：`ClientScene.FindLocalObject`(改用`NetworkIdentity.spawned[netId]`)。
