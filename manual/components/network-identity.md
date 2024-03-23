# 网络标识（Network Identity）

{% hint style="info" %}
另请参阅 [NetworkIdentity（网络标识）](https://storage.googleapis.com/mirror-api-docs/html/d3/d88/class\_mirror\_1\_1\_network\_identity.html) 在 API 参考中的内容。
{% endhint %}

网络标识组件是 Unity 网络高级 API 的核心。它控制游戏对象在网络中的唯一标识，并利用该标识使网络系统意识到游戏对象的存在。它提供了两种不同的配置选项，它们是互斥的，这意味着两个选项中的一个或者都不选。

* **仅服务器端**\
  &#x20;选中此复选框可确保 Unity 仅在服务器端生成游戏对象，而不在客户端生成。

![](<../../.gitbook/assets/image (50).png>)

{% hint style="danger" %}
需要注意的是，Mirror 不支持嵌套 GameObject 上的网络标识。否则，Mirror 将会发出错误。为避免此问题，请确保父 GameObject 是堆栈中唯一具有网络标识的 GameObject。子 GameObject 可以通过 Unity 内置的 GetComponentInParent 脚本函数访问父对象的网络标识组件。
{% endhint %}

## 实例化的网络游戏对象 <a href="#instantiated-network-game-objects" id="instantiated-network-game-objects"></a>

在 Mirror 的服务器授权网络系统中，服务器必须使用 `NetworkServer.Spawn` 生成具有网络标识的网络游戏对象。这会自动在连接到服务器的客户端上创建它们，并分配一个 `netId`。

对于在运行时生成的任何预制件，您必须在其上放置一个网络标识组件，以便网络系统使用它们。

## 基于场景的网络游戏对象 <a href="#scene-based-network-game-objects" id="scene-based-network-game-objects"></a>

您还可以网络化保存为场景一部分的游戏对象（例如，环境道具）。网络化游戏对象会使它们的行为略有不同，因为您需要让它们在网络中生成。

在构建游戏时，Unity 会禁用所有具有网络标识组件的基于场景的游戏对象。当客户端连接到服务器时，服务器会发送生成消息，告诉客户端要启用哪些场景游戏对象以及它们的最新状态信息是什么。这确保了客户端的游戏在开始游戏时不包含错误位置的游戏对象，或者 Unity 不会在连接时生成并立即销毁游戏对象（例如，如果事件在客户端连接之前移除了游戏对象）。有关更多信息，请参阅[网络化场景游戏对象](../guides/gameobjects/scene-gameobjects.md)。

## 预览窗格信息 <a href="#preview-pane-information" id="preview-pane-information"></a>

此组件包含网络跟踪信息，并在预览窗格中显示该信息。例如，场景 ID、网络 ID 和分配给对象的资产 ID。这使您能够检查信息，这对于调查和调试可能很有用。

![](<../../.gitbook/assets/image (132).png>)

在运行时，这里有更多信息要显示（禁用的 NetworkBehaviour 显示为非粗体）：

![](<../../.gitbook/assets/image (117).png>)
