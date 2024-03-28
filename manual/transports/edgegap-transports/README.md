# Edgegap 传输

[Edgegap的分布式中继](https://edgegap.com/en/platform/distributed-relay)服务在全球各地提供中继，允许玩家托管游戏而无需担心NAT或延迟问题。

要使用中继服务，您需要要么[设置自己的后端以按需启动中继会话](https://docs.edgegap.com/docs/relay-edgegap-api)，要么您可以使用Edgegap的[Lobby](https://docs.edgegap.com/docs/lobby/service)或[Matchmaker](https://docs.edgegap.com/docs/matchmaker)服务来为您执行此操作（作为额外奖励，您还可以获得大厅或匹配功能）。

Mirror提供以下传输方式与中继服务进行交互：

* [Edgegap Relay](edgegap-relay.md): 使用KCP的基本中继传输
* [Edgegap Lobby](edgegap-lobby.md): 使用Edgegap的Lobby服务的易于使用的中继传输
