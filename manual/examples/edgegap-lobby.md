---
description: >-
  你可以在你的 Mirror 项目中找到这个示例，位置在：Assets/Mirror/Examples/EdgegapLobby
---

# EdgegapLobby

这是[Tanks（坦克）](tanks.md)示例的副本（基本场景，玩家控制坦克），但添加了一个用于使用 Edgegap 的大厅和中继服务的大厅 UI。

它展示了如何与[EdgegapLobbyKcpTransport（边缘间隙大厅 KCP 传输）](../transports/edgegap-transports/#edgegaplobbykcptransport)进行交互以列出、加入和创建大厅，为任何想要使用 Edgegap 大厅的人提供了一个很好的起点。

## 设置

由于这个示例使用了来自 Edgegap 的外部服务，你需要在 NetworkManager 游戏对象上设置传输才能使用它，请查看[EdgegapLobbyKcpTransport 设置说明](../transports/edgegap-transports/#setup)。
