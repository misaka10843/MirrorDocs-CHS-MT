---
description: 展示 Mirror 内置 Room 系统的房间示例。
---

# 房间（Room）

{% hint style="success" %}
如果你在大堂里开派对，酒店工作人员会生气，他们会告诉你去找一个**房间**！
{% endhint %}

Mirror 的 Room 系统旨在成为独立的大堂服务之后的“下一步”，通常是一个具有匹配和游戏服务器启动功能的主服务器。

{% hint style="warning" %}
请注意，Mirror 的 Room 系统不允许游戏开始后晚加入者/旁观者。如果你需要这些功能，最好基于附加场景（Additive Scenes）来制作游戏。
{% endhint %}

## 初始设置

典型的设置涉及 3 个场景：

* 离线场景，通常是从一个独立服务中显示大堂的地方
* 房间场景，通常是游戏实例的玩家在开始游戏前集合和准备的地方
* 游戏场景，实际进行游戏的地方

{% hint style="info" %}
要使用 Room 系统制作自己的游戏，我们有[脚本模板](../general/script-templates.md)可供参考。
{% endhint %}

1. 创建 3 个空场景：离线、房间、游戏。如果已经有游戏场景，可以使用自己的游戏场景作为游戏场景。
2. 在离线和房间场景中，删除 Directional Light，并将 Main Camera 中的 Clear Flags 设置为 Solid Color（以便更容易看到默认 UI）。
3. 从脚本模板中创建自己的 Network Room Manager 和 Network Room Player 脚本。
4. 在离线场景中，创建一个名为 `RoomPlayer` 的空游戏对象，并附加刚创建的 Network Room Player 脚本，然后制作此对象的预制件并从场景中删除。
5. 在离线场景中，创建一个名为 `RoomManager` 的空游戏对象，并添加刚创建的 Network Room Manager 脚本，以及从“Add Component”菜单中添加的 Network Manager HUD 组件。
6. 在 Network Room Manager 的检视面板中，进行以下分配：
   * 将离线场景分配给 Offline Scene 字段
   * 将房间场景分配给**Online Scene**和**Room Scene**字段
   * 将游戏场景分配给 Gameplay 场景字段
   * 将 `RoomPlayer` 预制件分配给 Room Player Prefab 字段
   * 将自己的玩家预制件分配给 Player Prefab 字段
7. 将你的分配和设置与下面显示的检视面板进行比较。
8. 将所有 3 个场景添加到构建设置中，其中离线场景排在第一位，然后构建项目。
9. 打开离线场景，点击播放，然后在 HUD 中点击 Host 按钮。
10. 启动一个或多个已构建的客户端，并为每个客户端点击 HUD 中的 Client 按钮。
11. 当所有客户端都点击了各自的 Ready 按钮后，游戏场景将加载给每个人。
12. 在游戏场景中，主机客户端将看到一个返回房间的按钮，点击后所有人将返回到房间场景，以便与相同的玩家开始另一场游戏。房间玩家对象在游戏场景中不起作用，但不会被销毁，因此当返回到房间场景时它们将保持完整。

## 下一步

查看您从模板创建的 Network Room Manager 和 Network Room Player 脚本中的存根覆盖。它们有详细的注释说明了它们的用途。

您可以通过取消选中“显示房间 UI”复选框来关闭 Network Room Manager 和 Network Room Player 检查器中的默认 UI，然后为房间场景制作自己的 UI。

* 建议查看 [基本示例](basic.md) 中有关玩家 UI 的制作方式，以了解如何为每个玩家创建一个场景画布并注入本地实例化的 UI 部件。

![](<../../.gitbook/assets/image (121).png>)

![](<../../.gitbook/assets/image (30).png>)

![](<../../.gitbook/assets/image (38).png>)

![](<../../.gitbook/assets/image (67).png>)
