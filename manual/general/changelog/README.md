# 更改日志

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

## v90.x.x --进行中

### 添加

- 增加了：Transport：`IsEncrypted`和`EncryptionCypher`虚拟方法。
- 增加：加密传输实现`IsEncrypted`和`EncryptionCypher`。
- 新增：简单 Web 传输实现`IsEncrypted`和`EncryptionCypher`。
- 添加：边缘间隙大堂运输和演示。

### 固定

- 修正：Weaver 自定义读/写方法的序列化现在工作在整个程序集定义。
- 修复：EdgegapKcp StopHost（）空引用异常。
- 修正：ILPostProcessorAssemblyResolver 现在更好地忽略 Bee.BeeDriver。
- 固定：简单的 Web 传输现在本地范围`websocket`和`Runtime`。

### 改变

- 更改：继续改进`Prediction`和`PredictedRigidbody`.还在试验阶段

## v89.0.0 -- 2024-Mar-05

### 添加

- 添加：`GetFunctionMethodName`添加到`RemoteProcedureCalls`。
- 新增：` NetworkClient``RegisterHandler `现在采用`ChannelId`参数。
- 新增：`NetworkServer`和`NetworkClient`现在有带`ChannelID`参数`ReplaceHandler`。
- 新增：SyncVar 现在支持数组。
- 新增：`NetworkTime.SendPing`方法，用于`NetworkClient`从`OnTransportConnected`调用。
- 新增：NetworkTransform-Unreliable 仅发送更改的数据以提高带宽使用率。
- 添加：[滞后补偿器](../lag-compensation.md)作为方便的组件，包装所有滞后补偿逻辑。
- 添加：[加密传输](../../transports/encryption-transport.md)作为链式传输。

### 固定

- 修正：`NetworkServer.RemovePlayerForConnection`现在使用`NetworkConnectionToClient`。
- 修正：`NotReadyMessage`不再需要认证客户端。
- 修正：当多次调用时` InterestManagementBase``OnEnable `
- 修正：比赛和团队兴趣管理进行了大修，以获得更好的表现。
- 修正：`NetworkAnimator`现在在`OnEnable`中关闭状态。
- 修正：`NetworkAnimator`现在总是序列化和序列化的确切数量的数据。
- 修正：` NetworkAnimator``ClientRpc `现在有`includeOwner = false`。
- 修正：NetworkServer.SendToAll/Observers/ReadyObservers 现在验证包大小，而\<T>仍然是已知的。
- 修复：NetworkTransform-不可靠的四元数压缩。
- 修正：`NetworkManager.ServerChangeScene`现在阻止客户端调用。
- 修正：`NetworkManager`不再强制`networkAddress`在服务器构建时为`localhost`。
- 修正：多路复用器现在避免`KeyNotFoundException`。
- 修正：NetworkServer 和 NetworkClient 尊重异常 Disconnect。
- 修正：现在使用`FindAnyObjectByType`为以后的 Unity 版本。
- 修正：`NetworkServer.Destroy`重构.
- 固定：`NetworkServer.Spawn`现在激活不活动的游戏对象（在案件未产生）。

### 改变

- 更改：KCP 传输更新到版本 1.40。
- 更改：`InterestManagement`现在将`ResetState`与`Reset`分开。
- 更改：`NetworkTransform`现在有单独`ResetStat`e 从`Reset`。
- 更改：`NetworkTransform`同步方向现在默认为客户端到服务器。
- 更改：`NetworkAnimator`同步方向现在默认为客户端到服务器。
- 更改：`NetworkRigidbody`同步方向现在默认为客户端到服务器。
- 已更改：`onlySyncOnChange`和`compressRotation`移动到`NetworkTransformBase`。
- 更改：对`Prediction`和`PredictedRigidbody`大量改进...还在试验阶段
- 已更改：示例已更新。
- 更改：[脚本模板](../script-templates.md)更新。
- 更改：Edgegap Hosting 更新。
- **重大更改：**` NetworkIdentity``visible `命名为`visibility`。
