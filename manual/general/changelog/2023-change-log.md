# 2023 变更日志

{% hint style="info"%}
镜像将在每月初发布到[资产存储中](https://assetstore.unity.com/packages/tools/network/mirror-129321)，除非某些关键问题导致延迟。
联系我们

{% hint style="info"%}
镜像使用语义版本控制，此处显示的版本是发布到资产存储的版本，偶尔会在存储提交之间的月中发生重大版本冲突，因此此处不单独显示。
联系我们

{% hint style="info"%}
有关版本及其特定更改日志的详细信息，请参阅 github 发布页面：\
[https://github.com/MirrorNetworking/Mirror/releases](https://github.com/MirrorNetworking/Mirror/releases)
联系我们

## v86.13.4 -- 2023 年 12 月 24 日

- 修正：在某些情况下，KCP 会泛洪超时警告。
- 修正：压缩旋转`NetworkTransform`现在正确地考虑本地或世界坐标。
- 修正：`NetworkPingDisplay`变宽了一点，这样 RTT 就不会被切断。
- 修正：`PredictedRigidbody`和材料处理的几个更新
- 修正：`PredictedRigidbody`视觉旋转插值现在可以正常工作。

## v86.13.0 -- 2023 年 12 月 12 日

- 新增：`NetworkDiagnosticsDebugger`记录发送和接收的消息。
- 新增：`NetworkPingDisplay`现在显示连接质量。
- 新增：`PredictedRigidbody`可视化插值对象。
- 添加：LayerMask 支持网络读取器/写入器
- 新增：网络管理器现在有切换到禁用自动启动无头服务器/客户端在编辑器。
- 新增：网络管理器现在有切换到不断开客户端的消息异常。
- 新增：网络管理器现在有一个"填充可生成的预制件"按钮。
- 添加：Utils 现在有`isWebGL`和`isDebug`。
- 添加：SimpleWebTransport 现在返回远程地址从反向代理时可用。
- 添加：已添加其他示例。
- 修正：Edgegap 托管注册表，图像名称和标签字符串被修剪，以避免尾随空格的错误。
- 修正：SyncVar 钩子调用不再在每次调用时实例化一个新的 Action 委托。
- 修正：Ping 消息现在包括场景哈希，以防止长时间场景加载后的高 RTT。
- 修正：` NetworkTransform``OnTeleport `不再调用`Reset`，这会导致`NT Reliable`增量不同步。
- 修正：` NetworkBehaviour``authority `现在正确评估主机客户端。
- 修正：`LatencySimulation`现在正确地将延迟应用于两个通道。
- 修正：消息 ID 哈希改进，以尽量减少冲突。
- 更改：EdgeGap 插件更新到版本 2。
- 更改：网络管理器自动启动无头被重构。
- 更改：多路传输现在是`PortTransport`。
- 更改：一些示例现在使用`NetworkTransformReliable`。
- 更改：`NetworkTransformReliable`现在可以切换旋转压缩。
- 更改：`NetworkTransformReliable`现在总是使用带宽节省。
- 更改：示例中的一些重复脚本合并到\_Common 文件夹。
- 已更改：`ConnectionQuality`已移动到`NetworkClient`。
- 更改：`GUIConsole`布局更新为不与`NetworkHUD`冲突。
  - 默认热键更改为 tick（`），以便在WebGL构建中更安全地使用。` `
- 更改：KCP 传输`ToString`现在包括端口。
- 改变：心灵感应传输`ToString`被简化和缩短。
- 更改：KCP 和心灵感应传输现在在`Available`检查中使用编译器符号。
- 更改：KCP 传输更新到版本 1.39。
- 变更：已更新各种示例。
- 更改：[脚本模板](../script-templates.md)已更新。
- **重大更改**
  - SimpleWebTransport 设置和日志记录已重构。
    - There are now separate port fields for server and clients so you don't have to change them for building server and client.
  - SimpleWebTransport 使用 cert.json 和 SSL 证书已在组件检查器中标记为过时。
    - This feature will be removed eventually. 反向代理应该始终实施，因为它的性能更好，更容易维护。

## v81.4.0 -- 2023 年 8 月 3 日

- 示例：添加了 BenchmarkIdle 演示，以使用 80%空闲对象进行测试
- 功能：NetworkTransform WorldScale 选项
- NetworkClient：improve"未找到同步消息的目标"警告
- 修复：SimpleWebTransport 条件删除，使用户可以看到错误 M...
- kcp2k V1.37 - 2019 -07-31
- Unity 2021.3.29 LTS 最新修复
- 功能：历史边界 MVP（#3563）
- 坦克合作社的例子，采取车辆通过镜子管理局控制.
- 修复：NetworkReader Readbits/Array/List 分配限制到前.
- 删除未使用的导入
- NetworkWriter：清理旧的 UIntDouble 注释
- NetworkReader/Writer 读/写列表：注释
- 修复：NetworkConnection"无法发送大于...的数据包"消息 n.
- 修复：NetworkMessages.MaxContentSize 现在考虑 channelId 而不是.
- 功能：恢复 NetworkServer.disconnectInactiveConnections（#3556）
- 修复：删除冗余的 RPC 缓冲。 节省了带宽但引入了
- 修复：NetworkIdentity.AssignAssetId（）现在正确保存。 修复一个 b.
- 修复：NetworkTime RTT 现在检查来自未来的恶意时间戳
- 压缩：删除不必要的 QuaternionElement 函数
- 压缩：更明显的 TenBitsMax 表示
- 功能：NetworkConnectionToClient.rtt via Ping & Pong Messages（#3545）
- 示例：默认发送速率从 30 Hz 增加到 60 Hz，用于同步...
- perf：默认发送速率从 30 Hz 增加到 60 Hz，供用户使用。
- 坦克演示：增加发送速率从 30 赫兹到 120 赫兹，以避免 rtt bein.
- NetworkBehaviour. Ontario：GetBullentInParent（bool）2020 support.
- NetworkIdentity：correct GetBidentInParent explanations（credit：F.
- NetworkIdentity：在构建中公开 AssetGuidToUint
- 修复：NetworkBehaviour. Ontario 无法找到父 NetworkIdentity 是.
- 修复：坦克演示：替换炮塔过时 NetworkTransform 与 Networ.
- perf：删除一些不必要的内联以优化指令缓存
- NetworkIdentity：将 AssetId 暴露给 Guid 转换函数
- 修复：2019 测试再次工作（#3547）
- 修复：NetworkBehaviour Ontario #ifdef 为 Unity 2020.3.19 调整.
- 针对\[SyncVar]名称为.的情况改进了#3525 的错误消息。
- 通过应用#3525 的解决方法修复 NetworkIdentityTests
- 修复：#3525 通过解释错误消息中的解决方案。
- NetworkClient OnGUI：更好的 rtt 显示
- 中断：NetworkTime.PingFrequency 重命名为 PingInterval。 让它...
- 修复最近修复后的 PrepareToSpawnSceneObjects 测试
- NetworkTime：更易读的 ping 间隔检查
- 修复：#3538 在 DestroyOwnedObjects（#.
- 修复：PrepareToSpawnSceneObjects 检查 netId 而不是 activeSelf \[f.]
- 功能：滞后补偿 V1（#3534）
- 测试：修复失败的 NetworkManagerTests，因为 Deepware Tested 测试没有...
- NetworkTime：rttVar 重命名为 rttVariance，以与旧 M...
- 修复：SnapshotInterpolation.Insert（）现在有一个 bufferLimit 来避免 ev.
- perf：NetworkTransformBase SortedLists 现在使用默认 ca 分配.
- 更新了自述文件：阐明激励措施，修复错别字，改进格式
- 功能：ConnectionQuality 启发式+回调+ GUI（#3526）
- 网络时间：重新引入往返时间差异，为连接做好准备。
- NetworkClient：initialBufferTime 用于调试、比较和调试。
- 坦克演示：始终显示时间插值 GUI，便于调试
- 修复：NetworkClient TimeInterpolation：add 'current' bufferTimeMultipl.
- 现在禁用失败的测试，所以至少项目编译时...
- 修复：韦弗 SyncVarAttributeReplacer 现在检测修改\[Syn.
- Weaver：SyncVarsity Replacer：pass Logging
- Weaver：SyncVarsity Replacer：语法和注释使其更明显
- 修复：Weaver 现在在嵌套类型定义中编织 NetworkBehavious
- 功能：Weaver 将'bool Weaved（）'添加到每个 NetworkBehaviour 中，这.
- 测试：删除未使用的"using"
- 修复：测试工作在 Unity 2022.2+ \[imer]
- Weaver ILPostProcessorAssemblyResolver：使用 GetFileNameWithoutExtension
- perf：Weaver ILPostProcessorAssemblyResolver FindFile searches dllnam.
- Weaver：ILPostProcessorAssemblyResolver FindFile 注释
- Weaver：ILPostProcessorAssemblyResolver caches FindFile for a 50x imp.
- perf：Weaver ILPostProcessorAssemblyResolver FindFile caches exeName，.
- Weaver ILPostProcessorAssemblyResolver：FindFile uses string name par.
- perf：Weaver ILPostProcessorAssemblyResolver uses ConcurrentDictionar.
- RigidbodyPhysics 演示：禁用"onlySyncOnChange"，直到修复#3519
- Examples/VR link
- Unbatcher：Unity 2019 修复
- 修复：批处理 VarInt 大小头添加到修复自定义序列化 Net.
- 功能：NetworkRigidbody Benchmark 演示
- 修复：NetworkRigidbody/2D 现在使用.target 的 Rigidbody 并支持 ch.
- NetworkRigidbody：MovePosition 覆盖注释
- 功能：NetworkMessageId 类型查找和日志功能，用于调试...
- Unbatcher：简化 GetNextMessage
- 功能：VarInt 大小预测
- NetworkMessages：保留 GetId 以方便使用
- 中断：NetworkTransform 重命名为 NetworkTransformUnreliable，用于...
- 删除重复的 NetworkRigidbody 脚本
- NetworkTransform：将所有相关脚本移动到一个 NetworkTransform 中...
- perf：NetworkRigidbody Reliable/Unreliable via NetworkTransform for s...
- 修复：NetworkBehaviourInspector 'had no target'警告
- 自述文件：信息安全指南
- NetworkTransform：将 Gizmos 显示为 WriteCube 而不是实体 Cube，用于.
- 刚体示例：灰色背景，以便我们更容易看到叠加
- 刚体示例：显示时间插值 GUI
- RigidbodyPhysics 示例：修复缺少组件的警告
- 修复：Weaver 运行时熔丝，以确保编织成功之前开始...
- 快照插值示例：自述文件
- 添加说明
- 修复 NetworkClientTests for Unity 2019
- NetworkMessages.Wraptool：改进日志消息，使其更容易.
- 修复：NetworkClient unbatcher 现在重置在主机模式连接以及.
- 取消批处理：只读字段
- 修复：KcpServer.停止现在清除连接，使他们不被携带过.
- 修复：修复 2020 年新 C#的使用
- feat：添加选项以获取反向代理背后的 IP

## v79.0.0 -- 2023 年 6 月 7 日

- 添加：Writer / Reader 支持`DateTime`数据类型。
- 新增：`NetworkRoomManager`-`ReadyStatusChange`现在是一个虚拟方法。
- 新增：现在可以禁用 pos / rot / scale 的`NetworkTransform`插值。
- 增加：`ExponentialMovingAverage`现在有一`Reset`方法。
- 新增：`NetworkClient`的快照插值设置暴露在网络管理器中。
- 修正：`NetworkBehaviour`SyncVar 使用`netId`查找。
- 修正：不再为 SyncVars 使用自定义`NetworkBehaviour`编写器。
- 修正：非衍生`NetworkBehaviour`的 Writer 只为`netId`写入 0。
- 修正：KCP 更新到 1.33 版本。
- 修正：简单的 Web 传输改进了日志记录。
- 修正：`NetworkTime`-`PingWindowSize`从 10 缩短为 6，并在`ExponentialMovingAverage`中使用。
- 修正：当域缓存被禁用时，NetworkLoop 不再添加两次功能。
- 修正：NetworkLoop 不再以编辑模式运行。
- 修正：网络消息现在使用静态 NetworkMessageId 类和静态 ushort Id。
- 修正：Weaver 现在为 RPC 编织预先计算的函数哈希。
- 修正：快照插值收到各种更新。
- 修复：NetworkTransform（可靠）收到各种性能更新，修复抖动。
- 更改：兴趣管理现在从抽象基类派生。
- 已更改：NetworkWriter - MaxStringLength 更改为 ushort. MaxValue。
- **突破性变化：**
  - 删除过时的 OnServerError / OnClientError。
  - 删除了旧的 Unity 2018 / 2019_3 编译器定义。
  - 删除 NetworkClient.serverIp。
  - AsmDef 文件更新和简化。

## v73.0.0 -- 2023 年 2 月 9 日

- 新增：`NetworkClient.OnGUI`现在包含`bufferTimeMultiplier`的 BTM。
- 修正：客户端对象现在在产生处理程序之前被清除。
- 修正：`NetworkIdentity`不再重置`SyncObjects`。
  - 您可以自己在 Sync\* 集合上调用 Reset 方法。
- 固定：InitSyncObject Isrecordable/IsRecording 权限固定为主机模式和客户端上的其他播放器对象。
- 固定：同步\* 集合现在可以修改之前再次产卵。
- 修正：客户端同步\* 集合现在可以在离线时修改。
- 修正：处理程序不再为 Sync\* 集合双重触发。
- 固定：OP_SET 索引参数的 SyncList 处理程序固定。
- 固定：`SpawnObjects`现在激活，但不产卵下不活跃的父对象。
- 修正：`NetworkTransformBase`现在检查 OnGUI 中缺少的主摄像头。
- 修正：`NetworkTransformBase`现在重置时，改变客户端到服务器模式的权限。
- 修正：`NetworkTransformBase`OnClientAuthorityChanged 仅适用于服务器。
- 修正：`NetworkTransformReliable`现在正确使用`localScale`同步比例。
- 修正：`NetworkTransformReliable`现在检查以前的更改应用后的更改。
- 修复：`NetworkTransformReliable`发送最后一个已知快照时的抖动。
- 修正：`NetworkServer.DisconnectAll`不再将 active 设置为 false.它会切断所有的连接
  - `Shutdown`仍然正确地清理所有对象并将 active 设置为 false。
- 修正：重置`NetworkClient`和`NetworkServer`中的静态数据。
- 修正：`ChangeOwner`消息处理程序现在添加/删除客户端连接`owned`HashSet。
- 修正：`NetworkClient`现在在`OnStartAuthority`/`OnStartClient`被调用之前设置`NetworkIdentity.connectionToServer`。
- 修正：`isClient`和`isOwned`现在在`OnStartAuthority`中正确设置。
- 修正：当涉及利息管理时，`isClient`和`isOwned`现在正确设置。
- 修正：KCP 更新到 1.29 版本。
- 修正：OnClientDisconnect 现在可以正确调用 DNS 解析失败。
- 修正：从 KCP 程序集定义文件中删除无效引用。
- 修正：在镜像.传输程序集定义文件中添加了 SimpleWebTransport。
- 修正：大多数示例都进行了审查和更新，改进了 PlayerController。
- 修正：距离兴趣管理现在缓存引用自定义距离组件。
- 更改：`OnClientConnect`现在在任何场景更改之前，只要经过身份验证就会触发。
- 已更改：对所有组件定义文件使用已启用的"组件"。
- 更改：网络管理器 HUD 布局现在有点宽，减少包装。
- 更改：网络客户端中的 OnGUI 现在是公开的。
- 变更：已剔除覆盖过去 12 个月的符号，并将继续剔除到滚动的 12 个月时间段。
- 更改：延迟模拟传输配置已修改。
- 突破性的变化：旧的过时的属性和方法已被删除。
