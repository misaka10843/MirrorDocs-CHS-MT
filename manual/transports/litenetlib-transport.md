---
description: https://github.com/MirrorNetworking/LiteNetLibTransport/
---

# LiteNetLib 传输 (Transport)

**基于 LiteNetLib 的 Mirror 传输组件。**

## 使用方法 (Usage) <a href="#usage" id="usage"></a>

1. 从 [Releases](https://github.com/MirrorNetworking/LiteNetLibTransport/releases) 下载 Unity 软件包，并将其导入到您的项目中（不包含 Mirror）。
2. 将 `LiteNetLibTransport` 组件放置在带有 NetworkManager 的游戏对象上，并在那里分配它。

## 特性 (Features) <a href="#features" id="features"></a>

* UDP
* 内置网络发现和 UPnP
* 完全托管的代码
* CPU 和 RAM 使用率低
* 小的数据包大小开销（不可靠的 1 字节，可靠的数据包 3 字节）
* 不同的发送机制
* 有序可靠
* 无序可靠
* 有序但无法重复的不可靠
* 简单的无序和不可靠的 UDP 数据包
* 自动合并小数据包
* 可靠数据包的自动分段
* 自动 MTU 检测
* NTP 时间请求
* 数据包丢失和延迟模拟
* IPv6 支持（双模式）
* 连接统计信息（需要 DEBUG 或 STATS_ENABLED 标志）
* 多播（用于在本地网络中发现主机）

## IL2CPP 警告！ (IL2CPP Warning!) <a href="#il2cpp-warning" id="il2cpp-warning"></a>

使用 IL2CPP，IPv6 仅在 Unity 2018.3.6f1 及更高版本中受支持，原因如下：\
&#x20;[Unity ChangeLog](https://unity3d.com/unity/whats-new/2018.3.6)

> IL2CPP: 在 Windows 上添加了对 IPv6 的协议支持。 (1099133)
>
> IL2CPP: 正确指示在非 IPv6 平台上不支持 IPv6。 (1108823)

此外，在 IL2CPP 中，套接字重用地址选项不可用。

## 鸣谢 (Credits) <a href="#credits" id="credits"></a>

RevenantX - 为 [LiteNetLib](https://github.com/RevenantX/LiteNetLib/releases) 贡献\
&#x20;vis2k & Paul - 为 [Mirror](https://assetstore.unity.com/packages/tools/network/mirror-129321) 贡献\
&#x20;Coburn - 为 [Ignorance](https://github.com/SoftwareGuy/Ignorance) 贡献，我将其用作示例\
&#x20;Dankrushen - 帮助我找到了一个我两天找不到的小错误\
&#x20;Lucas Ontivero - 为 [Open.Nat](https://github.com/lontivero/Open.NAT/releases) 贡献，用于 UPnP\
&#x20;shiena - 为 NetworkDiscoveryHUD 贡献

请粘贴您要翻译的 Markdown 内容。
