# NetworkBehaviour 回调

## NetworkBehaviour 回调 <a href="#networkbehaviour-callbacks" id="networkbehaviour-callbacks"></a>

{% hint style="info" %}
另请参阅 [NetworkBehaviour](https://storage.googleapis.com/mirror-api-docs/html/db/d21/class\_mirror\_1\_1\_network\_behaviour.html) 在 API 参考中的内容。
{% endhint %}

在正常的多人游戏过程中，与网络行为相关的许多事件可能会发生。这些事件包括主机启动、玩家加入或玩家离开等。每个可能发生的事件都有一个关联的回调函数，您可以在自己的代码中实现以在事件发生时采取行动。

当您创建一个从 `NetworkBehaviour` 继承的脚本时，您可以编写自己的实现，以处理这些事件发生时应该发生的情况。为此，您需要重写 `NetworkBehaviour` 类上的虚拟方法，使用您自己的实现来处理给定事件发生时应该发生的情况。

这是您可以在 `NetworkBehaviour` 上实现的所有虚拟方法（回调）的完整列表，以及它们被调用的位置

### 仅服务器端 <a href="#server-only" id="server-only"></a>

* OnStartServer
  * 当行为在服务器上生成时调用
* OnStopServer
  * 当行为在服务器上被销毁或取消生成时调用
* OnSerialize
  * 在将行为发送到客户端之前对其进行序列化时调用，当重写时，请确保调用 `base.OnSerialize`

### 仅客户端 <a href="#client-only" id="client-only"></a>

* OnStartClient
  * 当行为在客户端生成时调用
* OnStartAuthority
  * 当行为在生成时具有权限时调用（例如本地玩家）
  * 当服务器授予行为权限时调用
* OnStartLocalPlayer
  * 当行为在本地玩家对象上时调用
* OnStopAuthority
  * 当对象被剥夺权限时调用（例如本地玩家被替换但未被销毁）
* OnStopClient
  * 当对象在客户端上被 `ObjectDestroyMessage` 或 `ObjectHideMessage` 消息销毁时调用

## 示例流程 <a href="#example-flows" id="example-flows"></a>

以下是不同模式的示例调用顺序

> 注意: `Start`在第一帧之前由Unity调用，通常在Mirror的回调之后调用。但是，如果您在`instantiate`的同一帧中没有调用`NetworkServer.Spawn`，则`Start`可能会首先被调用

> 注意: `OnRebuildObservers`和`OnSetHostVisibility`现在在`NetworkVisibility`(网络可见性)中，而不是在`NetworkBehaviour`(网络行为)中

### 服务器模式(Server mode) <a href="#server-mode" id="server-mode"></a>

当调用NetworkServer.Spawn时(例如，当新客户端连接并创建玩家时)

* `OnStartServer`
* `OnRebuildObservers`
* `Start`

### 客户端模式(Client mode) <a href="#client-mode" id="client-mode"></a>

当为客户端生成本地玩家时

* `OnStartAuthority`
* `OnStartClient`
* `OnStartLocalPlayer`
* `Start`

### 主机模式(Host mode) <a href="#host-mode" id="host-mode"></a>

这些仅在**玩家游戏对象**(Player Game Objects)上在客户端连接时调用:

* `OnStartServer`
* `OnRebuildObservers`
* `OnStartAuthority`
* `OnStartClient`
* `OnSetHostVisibility`
* `OnStartLocalPlayer`
* `Start`
