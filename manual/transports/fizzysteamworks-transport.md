---
description: https://github.com/Chykary/FizzySteamworks/
---

# FizzySteamworks 传输 (Transport)

FizzySteamworks 是 Mirror 的 Steam P2P 传输，它利用 Steam 的 P2P 服务直接连接或中继您与另一玩家的连接。FizzySteamworks 基于 [Steamworks.Net](https://github.com/rlabrecque/Steamworks.NET) 封装器。

您可以在[**这里**](https://github.com/Chykary/FizzySteamworks/releases)获取包含最新版本 Steamworks.Net 的发布版，或者您可以克隆仓库[**这里**](https://github.com/Chykary/FizzySteamworks)。

## 特性 (Features) <a href="#features" id="features"></a>

* 多个可定制通道 (Channels)：您可以在传输中自定义通道，无论您想要 1 个还是 5 个不可靠或可靠的通道 (最好将通道 0 设置为可靠)。
* Steam Nat Punching & Relay：传输将使用 Steam 对目标进行 Nat Punching，如果这不起作用，Steam 的中继服务器将被用来确保您始终可以连接 (延迟可能会有所不同)。
* 无需更改代码：如果您已经在使用 Mirror，您只需将此传输添加进去 (也许在构建中添加您的 Steam 应用 ID)，一切应该像任何其他 Mirror 传输一样工作。"它只是起作用" -Todd Howard

![](<../../.gitbook/assets/image (106).png>)

## 鸣谢 (Credits) <a href="#credits" id="credits"></a>

* [Fizz Cube](https://github.com/FizzCube)：此传输的原始作者。
* [Chykary](https://github.com/Chykary/FizzySteamworks)：此传输的当前维护者。
* [rlabrecque](https://github.com/rlabrecque)：Steamworks.Net 的创建者。
* [vis2k](https://github.com/vis2k)：Mirror 的创建者。
* Valve：Steam
