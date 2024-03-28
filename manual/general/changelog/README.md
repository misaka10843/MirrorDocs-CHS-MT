# 更新日志（Change Log）

{% hint style="info" %}
Mirror 每个月初发布到 [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) ，除非某些关键问题导致延迟。
{% endhint %}

{% hint style="info" %}
Mirror 使用语义化版本控制，并且这里显示的版本是发布到 Asset Store 的版本，有时会在提交到商店之间发生主要版本升级，因此这里不会单独显示。
{% endhint %}

{% hint style="info" %}
有关版本及其具体更改日志的详细信息，请参阅 github 发布页面：\
[https://github.com/MirrorNetworking/Mirror/releases](https://github.com/MirrorNetworking/Mirror/releases)
{% endhint %}

## v90.x.x -- 进行中

### 添加（Added）

* 添加：Transport（传输）：`IsEncrypted` 和 `EncryptionCypher` 虚拟方法。
* 添加：Encryption Transport 实现`IsEncrypted` 和 `EncryptionCypher` 。
* 添加：Simple Web Transport 实现`IsEncrypted` 和 `EncryptionCypher` 。
* 添加：Edgegap Lobby Transport 和 Demo。

### 修复（Fixed）

* 修复：Weaver 自定义读写方法用于序列化现在跨程序集定义工作。
* 修复：EdgegapKcp StopHost() 空引用异常。
* 修复：ILPostProcessorAssemblyResolver 现在更好地忽略 Bee.BeeDriver。
* 修复：Simple Web Transport 现在本地范围 `websocket` 和 `Runtime`。

### 更改（Changed）

* 更改：持续改进 `Prediction` 和 `PredictedRigidbody`...仍处于实验阶段。

## v89.0.0 -- 2024年3月5日

### 添加（Added）

* 添加：`GetFunctionMethodName` 添加到 `RemoteProcedureCalls`。
* 添加：`NetworkClient` `RegisterHandler` 现在带有 `ChannelId` 参数。
* 添加：`NetworkServer` 和 `NetworkClient` 现在具有带有 `ChannelID` 参数的 `ReplaceHandler`。
* 添加：SyncVar 现在支持数组。
* 添加：`NetworkTime.SendPing` 方法供 `NetworkClient` 从 `OnTransportConnected` 调用。
* 添加：NetworkTransform-Unreliable 仅发送更改数据以改善带宽使用情况。
* 添加：[Lag Compensator](../lag-compensation.md) 作为包装所有滞后补偿逻辑的便利组件。
* 添加：[Encryption Transport](../../transports/encryption-transport.md) 作为链式传输。

### 修复

* 修复：`NetworkServer.RemovePlayerForConnection` 现在使用 `NetworkConnectionToClient`。（原文：`NetworkServer.RemovePlayerForConnection` now uses `NetworkConnectionToClient`）
* 修复：`NotReadyMessage` 不再需要经过身份验证的客户端。（原文：`NotReadyMessage` no longer requires authenticated client）
* 修复：`InterestManagementBase` 的 `OnEnable` 在多次调用时不再显示错误。（原文：`InterestManagementBase` `OnEnable` no longer shows error when called multiple times）
* 修复：为了更好的性能，Match 和 Team Interest Management 进行了全面改进。（原文：Match and Team Interest Management overhauled for better performance）
* 修复：`NetworkAnimator` 现在在 `OnEnable` 中初始化状态。（原文：`NetworkAnimator` now initializes state in `OnEnable`）
* 修复：`NetworkAnimator` 现在始终序列化和反序列化确切数量的数据。（原文：`NetworkAnimator` now always serializes and deserializes the exact amount of data）
* 修复：`NetworkAnimator` 的 `ClientRpc` 现在 `includeOwner = false`。（原文：`NetworkAnimator` `ClientRpc` now has `includeOwner = false`）
* 修复：在\<T>仍然已知的情况下，`NetworkServer.SendToAll/Observers/ReadyObservers` 现在验证数据包大小。（原文：`NetworkServer.SendToAll/Observers/ReadyObservers` now validates packet size while \<T> is still known）
* 修复：修复了 `NetworkTransform` 的不可靠四元数压缩。（原文：Fixed: `NetworkTransform-Unreliable Quaternion compression`）
* 修复：`NetworkManager.ServerChangeScene` 现在阻止客户端调用。（原文：`NetworkManager.ServerChangeScene` now prevents client from calling）
* 修复：在服务器构建中，`NetworkManager` 不再强制 `networkAddress` 为 `localhost`。（原文：`NetworkManager` no longer forces `networkAddress` to `localhost` on server builds）
* 修复：Multiplexer 现在避免 `KeyNotFoundException`。（原文：Multiplexer now avoids `KeyNotFoundException`）
* 修复：`NetworkServer` 和 `NetworkClient` 尊重 `exceptionDisconnect`。（原文：NetworkServer and NetworkClient respect exceptionDisconnect）
* 修复：现在在后续 Unity 版本中使用 `FindAnyObjectByType`。（原文：Now uses `FindAnyObjectByType` for later Unity versions）
* 修复：重构了 `NetworkServer.Destroy`。（原文：`NetworkServer.Destroy` refactored）
* 修复：`NetworkServer.Spawn` 现在激活未激活的 GameObjects（以防未生成）。（原文：`NetworkServer.Spawn` now activates inactive GameObjects (in case Unspawned)）

### 更改

* 更改：KCP 传输更新到版本 1.40。（原文：Changed: KCP Transport updated to version 1.40）
* 更改：`InterestManagement` 现在有单独的 `ResetState` 和 `Reset`。（原文：Changed: `InterestManagement` now has separate `ResetState` from `Reset`）
* 更改：`NetworkTransform` 现在有单独的 `ResetState` 和 `Reset`。（原文：Changed: `NetworkTransform` now has separate `ResetState` from `Reset`）
* 更改：`NetworkTransform` 同步方向现在默认为客户端到服务器端。（原文：Changed: `NetworkTransform` Sync Direction now defaults to Client To Server）
* 更改：`NetworkAnimator` 同步方向现在默认为客户端到服务器端。（原文：Changed: `NetworkAnimator` Sync Direction now defaults to Client To Server）
* 更改：`NetworkRigidbody` 同步方向现在默认为客户端到服务器端。（原文：Changed: `NetworkRigidbody` Sync Direction now defaults to Client To Server）
* 更改：`onlySyncOnChange` 和 `compressRotation` 移动到 `NetworkTransformBase`。（原文：Changed: `onlySyncOnChange` and `compressRotation` moved to `NetworkTransformBase`）
* 更改：对 `Prediction` 和 `PredictedRigidbody` 进行了广泛改进...仍然是实验性的。（原文：Changed: Extensive improvements to `Prediction` and `PredictedRigidbody`...still experimental）
* 更改：更新示例。（原文：Changed: Examples Updated）
* 更改：[脚本模板](../script-templates.md) 已更新。（原文：Changed: [Script Templates](../script-templates.md) Updated）
* 更改：Edgegap Hosting 已更新。（原文：Changed: Edgegap Hosting Updated）
* **重大更改：** `NetworkIdentity` 的 `visible` 更名为 `visibility`。（原文：**Breaking Change:** `NetworkIdentity` `visible` renamed to `visibility`）

请粘贴您要翻译的 Markdown 内容。
