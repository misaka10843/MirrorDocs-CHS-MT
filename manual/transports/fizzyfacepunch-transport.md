---
description: https://github.com/Chykary/FizzyFacepunch/
---

# FizzyFacepunch 传输 (Transport)

FizzyFacepunch 是 Mirror 的 Steam P2P 传输插件，它利用 Steam 的 P2P 服务直接连接或中继您与另一玩家的连接。FizzyFacepunch 基于 [Facepunch.Steamworks](https://github.com/Facepunch/Facepunch.Steamworks) 封装。

您可以在这里获取发布版本 [**Here**](https://github.com/Chykary/FizzyFacepunch/releases)，或者您可以克隆仓库 [**Here**](https://github.com/Chykary/FizzyFacepunch)。

## 特性 (Features) <a href="#features" id="features"></a>

* 多个可自定义通道 (Channels)：您可以在传输中自定义通道，无论您只想要 1 个还是 5 个不可靠或可靠的通道 (最好将通道 0 设置为可靠)。
* Steam Nat Punching & Relay：传输将使用 Steam 对目标进行 Nat Punching，如果这不起作用，将使用 Steam 的中继服务器来确保您始终可以连接 (延迟可能有所不同)。
* 无需更改代码：如果您已经在使用 Mirror，您只需将此传输插件添加进去 (可能需要在构建中添加您的 Steam 应用程序 ID)，一切应该像任何其他 Mirror 传输插件一样工作。"它只是起作用" -Todd Howard

![](<../../.gitbook/assets/image (28).png>)

## 鸣谢 (Credits) <a href="#credits" id="credits"></a>

* [Chykary](https://github.com/Chykary/FizzyFacepunch)：本传输插件的作者。
* [Facepunch](https://github.com/Facepunch)：Facepunch.Steamworks 的创建者。
* [vis2k](https://github.com/vis2k)：Mirror 的创建者。
* Valve：Steam
