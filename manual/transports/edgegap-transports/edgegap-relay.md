# Edgegap Relay(边缘缺口中继)

`EdgegapKcpTransport`是一个简单的传输工具，通过Edgegap的分布式中继使用[KCP](../kcp-transport.md)(KCP传输)。

它期望您自行设置中继会话并与所有玩家通信详细信息。

一旦您完成设置，设置`relayAdddress`(中继地址), `sessionId`(会话ID), `userId`(用户ID)以及相应的`relayGameClientPort`(中继游戏客户端端口)或`relayGameServerPort`(中继游戏服务器端口)，具体取决于玩家是否托管。

填写完详细信息后，只需像往常一样通过NetworkManager实例启动客户端/服务器/主机即可。
