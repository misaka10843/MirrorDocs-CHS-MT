# 2023 更新日志

{% hint style="info" %}
Mirror 每个月初发布到 [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321)，除非某些关键问题导致延迟。
{% endhint %}

{% hint style="info" %}
Mirror 使用语义化版本控制，这里显示的版本是发布到 Asset Store 的版本，有时会在商店提交之间发生主要版本升级，因此这里不会单独显示。
{% endhint %}

{% hint style="info" %}
有关版本及其具体更新日志的详细信息，请参阅 github 发布页面：\
[https://github.com/MirrorNetworking/Mirror/releases](https://github.com/MirrorNetworking/Mirror/releases)
{% endhint %}

## v86.13.4 -- 2023年12月24日

* 修复：在某些条件下，KCP 会导致超时警告泛滥。
* 修复：在 `NetworkTransform` 上压缩旋转现在正确考虑本地或世界坐标。
* 修复：`NetworkPingDisplay` 稍微加宽，以防止 RTT 被截断。
* 修复：对 `PredictedRigidbody` 和材质处理进行了几处更新。
* 修复：`PredictedRigidbody` 的可视旋转插值现在正常工作。

## v86.13.0 -- 2023年12月12日

* 新增：`NetworkDiagnosticsDebugger` 用于记录发送和接收的消息。
* 新增：`NetworkPingDisplay` 现在显示连接质量。
* 新增：`PredictedRigidbody` 可视插值对象。
* 新增：在 Network Reader / Writer 中添加 LayerMask 支持。
* 新增：Network Manager 现在有一个开关来禁用编辑器中自动启动 Headless 服务器 / 客户端。
* 新增：Network Manager 现在有一个开关，遇到消息异常时不断开客户端。
* 新增：Network Manager 现在有一个 Populate Spawnable Prefabs 按钮。
* 新增：Utils 现在有 `isWebGL` 和 `isDebug`。
* 新增：SimpleWebTransport 现在在可用时从反向代理返回远程地址。
* 新增：添加了额外的示例。
* 修复：Edgegap Hosting 注册表、图像名称和标签字符串被修剪以避免尾随空格导致错误。
* 修复：SyncVar 钩子调用不再在每次调用时实例化新的 Action 委托。
* 修复：Ping 消息现在包含场景哈希以防止长场景加载后高 RTT。
* 修复：`NetworkTransform` 的 `OnTeleport` 不再调用 `Reset`，这会导致 `NT Reliable` 的增量不同步。
* 修复：`NetworkBehaviour` 的 `authority` 现在正确评估主机客户端。
* 修复：`LatencySimulation` 现在正确应用延迟到两个通道。
* 修复：消息 ID 哈希改进以最小化冲突。
* 更改：EdgeGap 插件更新到版本 2。
* 更改：Network Manager Auto-Start Headless 重构。
* 更改：Multiplex Transport 现在是一个 `PortTransport`。
* 更改：一些示例现在使用 `NetworkTransformReliable`。
* 更改：`NetworkTransformReliable` 现在有旋转压缩的开关。
* 更改：`NetworkTransformReliable` 现在始终使用带宽节省。
* 更改：一些示例中重复的脚本合并到 \_Common 文件夹。
* 更改：`ConnectionQuality` 移动到 `NetworkClient`。
* 更改：`GUIConsole` 布局更新，以避免与 `NetworkHUD` 冲突。
  * 默认热键更改为反引号（`` ` ``），以在 WebGL 构建中更安全地使用。
* 更改：KCP Transport 的 `ToString` 现在包括端口。
* 更改：Telepathy Transport 的 `ToString` 已简化和缩短。
* 更改：KCP 和 Telepathy 传输现在在 `Available` 检查中使用编译器符号。
* 更改：KCP Transport 更新到版本 1.39。
* 更改：更新了各种示例。
* 更改：[脚本模板](../script-templates.md) 已更新。
* **重大变更**
  * SimpleWebTransport 设置和日志已重构。
    * 现在服务器和客户端有单独的端口字段，因此您不必为构建服务器和客户端而更改它们。
  * SimpleWebTransport 使用 cert.json 和 SSL 证书已在组件检查器中标记为过时。
    * 此功能最终将被移除。始终应实现反向代理，因为它的性能更好且更易于维护。

## v81.4.0 -- 2023年8月3日

* 示例: 添加了BenchmarkIdle演示，用于测试80%空闲对象
* 功能: NetworkTransform世界缩放选项
* NetworkClient: 改进了“未找到同步消息目标”的警告
* 修复: SimpleWebTransport条件已移除，用户可以看到错误消息...
* kcp2k V1.37 \[2023-07-31]
* Unity 2021.3.29 LTS 以获取最新修复
* 功能: HistoryBounds MVP (#3563)
* 坦克合作示例，通过Mirror的Authority控制车辆...
* 修复: NetworkReader ReadBytes/Array/List 分配限制为...
* 移除未使用的导入
* NetworkWriter: 清理旧的UIntDouble注释
* NetworkReader/Writer Read/WriteList: 注释
* 修复: NetworkConnection“无法发送大于…”消息
* 修复: NetworkMessages.MaxContentSize现在考虑channelId而不是...
* 功能: 恢复NetworkServer.disconnectInactiveConnections (#3556)
* 修复: 移除冗余的RPC缓冲。节省带宽，但引入了...
* 修复: NetworkIdentity.AssignAssetId()现在保存正确。修复了一个b...
* 修复: NetworkTime RTT现在检查来自未来的恶意时间戳
* 压缩: 移除不必要的QuaternionElement函数
* 压缩: 更明显的TenBitsMax表示
* 功能: NetworkConnectionToClient通过Ping和Pong消息获取rtt (#3545)
* 示例: 默认发送速率从30 Hz增加到60 Hz以保持...
* 性能: 默认发送速率从30 Hz增加到60 Hz，以便用户获得...
* 坦克演示: 将发送速率从30 Hz增加到120 Hz以避免rtt...
* NetworkBehaviour.OnValidate: GetComponentInParent(bool) 2020支持...
* NetworkIdentity: 修正GetComponentInParent解释（来源: F...）
* NetworkIdentity: 在构建中公开AssetGuidToUint
* 修复: NetworkBehaviour.OnValidate找不到父NetworkIdentity的...
* 修复: 坦克演示: 用NetworkTransform替换过时的Turret...
* 性能: 移除一些不必要的内联以优化指令缓存
* NetworkIdentity: 在构建中公开AssetId到Guid转换函数
* 修复: 2019测试再次可用 (#3547)
* 修复: NetworkBehaviour OnValidate #ifdef 调整为Unity 2020.3.19...
* 改进#3525的错误消息，用于\ [SyncVar]名称...
* 通过应用#3525的解决方案修复NetworkIdentityTests
* 修复: #3525通过在错误消息中解释解决方案。
* NetworkClient OnGUI: 更好的rtt显示
* 破坏性更改: NetworkTime.PingFrequency重命名为PingInterval。使...
* 修复最近修复后的PrepareToSpawnSceneObjects测试
* NetworkTime: 更易读的ping间隔检查
* 修复: #3538在DestroyOwnedObjects中调用场景对象上的unspawn (#...
* 修复: PrepareToSpawnSceneObjects检查netId而不是activeSelf \[f...
* 功能: 拉格补偿V1 (#3534)
* 修复因DeeplyTested测试未通过而导致NetworkManagerTests失败
* NetworkTime: rttVar重命名为rttVariance以与旧M一致...
* 修复: SnapshotInterpolation.Insert()现在具有缓冲限制以避免...
* 性能: NetworkTransformBase SortedLists现在使用默认ca...
* 更新自述文件: 澄清激励，修正拼写错误，改进格式
* 功能: 连接质量启发式+回调+GUI (#3526)
* NetworkTime: 重新引入往返时间方差以准备Conn...
* NetworkClient: 用于调试、比较和...
* 坦克演示: 始终显示时间插值GUI以便更轻松调试
* 修复: NetworkClient TimeInterpolation: 添加'current' bufferTimeMultipl...
* 暂时禁用失败的测试，以便至少项目可以编译w...
* 修复: Weaver SyncVarAttributeAccessReplacer现在检测修改\ [Syn...
* Weaver: SyncVarAccessReplacer: 传递日志
* Weaver: SyncVarAccessReplacer: 语法和注释使其更明显
* 修复: Weaver现在在嵌套类型定义中编织NetworkBehavious
* 功能: Weaver为每个NetworkBehaviour添加'bool Weaved()'，该函数...
* 测试: 移除未使用的'using'
* 修复: 在Unity 2022.2+上运行测试 \[imer]
* Weaver ILPostProcessorAssemblyResolver: 使用GetFileNameWithoutExtension
* 性能: Weaver ILPostProcessorAssemblyResolver FindFile搜索dllnam...
* Weaver: ILPostProcessorAssemblyResolver注释
* Weaver: ILPostProcessorAssemblyResolver为50x imp...缓存FindFile
* 性能: Weaver ILPostProcessorAssemblyResolver FindFile缓存exeName,...
* Weaver ILPostProcessorAssemblyResolver: FindFile使用字符串名称par...
* 性能: Weaver ILPostProcessorAssemblyResolver使用ConcurrentDictionar...
* RigidbodyPhysics演示: 在#3519修复之前禁用'onlySyncOnChange'
* 示例/VR链接
* Unbatcher: Unity 2019修复
* 修复: 添加了批处理VarInt大小标头以修复自定义序列化Net...
* 功能: NetworkRigidbody基准演示
* 修复: NetworkRigidbody/2D现在使用.target的Rigidbody并支持ch...
* NetworkRigidbody: MovePosition覆盖注释
* 功能: NetworkMessageId <> 类型查找和用于调试的日志函数...
* Unbatcher: 简化GetNextMessage
* 功能: VarInt大小预测
* NetworkMessages: 保留GetId以方便使用
* 破坏性更改: NetworkTransform重命名为NetworkTransformUnreliable以...
* 移除重复的NetworkRigidbody脚本
* NetworkTransform: 将所有相关脚本移动到一个NetworkTransform...
* 性能: NetworkRigidbody可靠/不可靠通过NetworkTransform进行s...
* 修复: NetworkBehaviourInspector“没有目标”的警告
* 自述文件: 信息安全指南
* NetworkTransform: 将Gizmos显示为WriteCube而不是实心Cube以...
* Rigidbody示例: 灰色背景，以便更容易看到叠加
* Rigidbody示例: 显示时间插值GUI
* RigidbodyPhysics示例: 修复缺少组件警告
* 修复: Weaver运行时保险丝以确保编织成功后才启动...
* Snapshot Interpolation示例: 自述文件
* CompilationFinishedHook: 添加说明
* 修复Unity 2019的NetworkClientTests
* NetworkMessages.WrapHandler: 改进日志消息，使其更容易...
* 修复: NetworkClient unbatcher现在在主机模式连接时也会重置...
* Unbatcher: 只读字段
* 修复: KcpServer.Stop现在清除连接，以便它们不会被带到...
* 修复: 修复在2020中使用新c#的问题
* 功能: 添加选项以获取反向代理后面的IP

## v79.0.0 -- 2023年6月7日

* 新增：`DateTime` 数据类型的 Writer / Reader 支持。
* 新增：`NetworkRoomManager` - `ReadyStatusChange` 现在是一个虚方法（virtual method）。
* 新增：`NetworkTransform` 插值现在可以在位置（pos）/ 旋转（rot）/ 缩放（scale）上禁用。
* 新增：`ExponentialMovingAverage` 现在有一个 `Reset` 方法。
* 新增：`NetworkClient` 的快照插值设置在 Network Manager 中暴露。
* 修复：`NetworkBehaviour` SyncVar 使用 `netId` 查找。
* 修复：不再为 SyncVars 使用自定义的 `NetworkBehaviour` writers。
* 修复：非生成的 `NetworkBehaviour` 的 Writer 现在只为 `netId` 写入 0。
* 修复：KCP 更新至版本 1.33。
* 修复：Simple Web Transport 改进日志记录。
* 修复：`NetworkTime` - `PingWindowSize` 从 10 缩短到 6 并在 `ExponentialMovingAverage` 中使用。
* 修复：当域重新加载被禁用时，NetworkLoop 不再添加函数两次。
* 修复：NetworkLoop 不再在编辑模式下运行。
* 修复：网络消息现在使用静态的 NetworkMessageId 类和静态的 ushort Id。
* 修复：Weaver 现在为 RPCs 编织预先计算的函数哈希。
* 修复：快照插值收到各种更新。
* 修复：NetworkTransform（可靠）收到各种性能更新，修复抖动。
* 更改：兴趣管理现在派生自一个抽象基类。
* 更改：NetworkWriter - MaxStringLength 更改为 ushort.MaxValue。
* **重大更改：**
  * 移除过时的 OnServerError / OnClientError。
  * 移除旧的 Unity 2018 / 2019\_3 编译器定义。
  * 移除 NetworkClient.serverIp。
  * 更新和简化了 AsmDef 文件。

## v73.0.0 -- 2023年2月9日

* 新增：`NetworkClient.OnGUI` 现在包括 `bufferTimeMultiplier` 的 BTM。
* 修复：在生成处理程序之前现在清除客户端对象。
* 修复：`NetworkIdentity` 不再重置 `SyncObjects`。
  * 您可以在 Sync\* 集合上自行调用 Reset 方法。
* 修复：在主机模式和客户端上的其他玩家对象上修复了 InitSyncObject IsWritable/IsRecording 权限。
* 修复：再次生成之前现在可以修改 Sync\* 集合。
* 修复：客户端 Sync\* 集合现在可以在离线时修改。
* 修复：处理程序不再为 Sync\* 集合双重触发。
* 修复：SyncList 处理程序的 OP\_SET 索引参数修复。
* 修复：`SpawnObjects` 现在激活但不在非活动父级下生成对象。
* 修复：`NetworkTransformBase` 现在在 OnGUI 中检查缺失的主摄像机。
* 修复：在客户端到服务器模式中更改权限时，`NetworkTransformBase` 现在重置。
* 修复：`NetworkTransformBase` OnClientAuthorityChanged 仅服务器端执行。
* 修复：`NetworkTransformReliable` 现在正确使用 `localScale` 进行缩放同步。
* 修复：`NetworkTransformReliable` 现在在应用先前更改后检查更改。
* 修复：从 `NetworkTransformReliable` 发送最后已知快照的抖动修复。
* 修复：`NetworkServer.DisconnectAll` 不再将 active 设置为 false...只是断开所有连接。
  * `Shutdown` 仍然正确清理所有对象并将 active 设置为 false。
* 修复：在 `NetworkClient` 和 `NetworkServer` 中的静态重置。
* 修复：`ChangeOwner` 消息处理程序现在向客户端连接的 `owned` HashSet 添加/移除。
* 修复：`NetworkClient` 现在在调用 `OnStartAuthority` / `OnStartClient` 之前设置 `NetworkIdentity.connectionToServer`。
* 修复：在 `OnStartAuthority` 中现在正确设置 `isClient` 和 `isOwned`。
* 修复：在涉及兴趣管理时，现在正确设置 `isClient` 和 `isOwned`。
* 修复：KCP 更新至版本 1.29。
* 修复：现在对于 DNS 解析失败正确调用 OnClientDisconnect。
* 修复：从 KCP Assembly Definition 文件中移除无效引用。
* 修复：将 SimpleWebTransport 添加到 Mirror.Transports Assembly Definition 文件。
* 修复：大多数示例已经过审查和更新，改进了 PlayerController。
* 修复：距离兴趣管理现在缓存到自定义距离组件的引用。
* 更改：`OnClientConnect` 现在在认证后立即触发，而不是在任何场景更改之前。
* 更改：对所有 Assembly Definition 文件启用了使用 GUID。
* 更改：Network Manager HUD 布局现在更宽以减少换行。
* 更改：Network Client 中的 OnGUI 现在是公共的。
* 更改：编译器符号已被削减到覆盖过去12个月的符号，并将继续削减到滚动的12个月时间段。
* 更改：延迟模拟传输配置已进行了改进。
* **重大更改：** 移除了较旧的已弃用属性和方法。

请提供要翻译的 Markdown 内容。
