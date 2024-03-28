# 传输方式(Transports)

## 内置传输方式(Built-in Transports)

这些传输方式已经包含在 Mirror 中。

* [KCP](kcp-transport.md) - 基于 kcp.c 的 UDP 传输方式，逐行翻译为 C#(C#)
* [Telepathy](telepathy-transport.md) - 简单的、基于消息的、适用于大规模 MMO 的 TCP 网络传输方式(C#)
* [Simple Web Sockets](websockets-transport/) - 为 Mirror 提供针对浏览器客户端的 WebGL 传输层(C#)
* [Multiplexer](multiplex-transport.md) - 桥接传输方式，允许服务器同时处理不同传输方式的客户端，例如桌面客户端使用 Telepathy 与 Web 客户端使用 Websockets(C#)
* [Latency Simulation](latency-simulaton-transport.md) - 中间传输方式，用于测试非理想的网络条件(C#)
* [Encryption](encryption-transport.md) - 中间传输方式，用于对不同传输方式进行加密(C#)
* [Edgegap Relay Transports](edgegap-transports/) - 用于利用 [Edgegaps Destributed Relay](https://edgegap.com/en/platform/distributed-relay) 服务的传输方式

## 附加传输方式(Additional Transports)

这些传输方式由 Mirror 之外的第三方维护。

* [Monke](https://github.com/JesusLuvsYooh/monke) - 用于 Mirror 的即插即用的加密中间传输层(C#)
* [Ignorance](ignorance.md) - 基于 ENet 的可靠和不可靠的序列化 UDP 传输方式(C#)
* [LiteNetLibTransport](litenetlib-transport.md) - 基于 [LiteNetLib](https://github.com/RevenantX/LiteNetLib) 的 UDP 传输方式(C#)

## 中继传输方式(Relay Transports)

这些传输方式由第三方维护，并使用中继基础设施将客户端连接到位于防火墙/NAT 后的服务器。

* [Steam - FizzySteamworks](fizzysteamworks-transport.md) - 利用 Steam P2P 网络的传输方式，基于 [Steamworks.NET](https://github.com/rlabrecque/Steamworks.NET)(C#)
* [Epic - Epic Online Services](https://github.com/FakeByte/EpicOnlineTransport) - 利用 Epic 的免费中继服务的中继传输方式(C#)
* [LRM - Light Reflective Mirror](https://github.com/Speidy674/Light-Reflective-Mirror) - 用于 WebGL 客户端的中继传输方式(C#)

## 更改传输方式

更改传输方式非常简单，只需要几个步骤：

* 前往具有 Network Manager 组件的游戏对象
* 通过“添加组件”按钮添加另一个传输脚本
* 将传输脚本拖动到 Network Manager 中的“Transport”字段
* 移除旧的传输脚本（可选）

如果您在使用需要端口转发的传输时遇到连接问题，请确保正确地为所需协议（TCP / UDP）进行端口转发。
