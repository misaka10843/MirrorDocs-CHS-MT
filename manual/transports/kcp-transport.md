---
description: https://github.com/vis2k/kcp2k
---

# KCP 传输 (Transport)

**Mirror 的 KCP 传输 (KCP transport).**

kcp2k 是 Mirror 的新默认传输 (Transport)。

![在检视器窗口中的 KCP 传输 (Transport) 组件](<../../.gitbook/assets/image (120).png>)

### 关于这个传输 (Transport) <a href="#about-this-transport" id="about-this-transport"></a>

为什么选择 KCP: [https://github.com/skywind3000/kcp/blob/master/README.en.md](https://github.com/skywind3000/kcp/blob/master/README.en.md)

* 100% C# (C#).
* 适用于除了 WebGL 之外的所有平台。
* 高测试覆盖率 (83.5%)。
* 极其快速。
* 极其简单。
* 几乎无需分配内存\*。

"KCP 是一种快速可靠的协议，可以实现平均延迟的降低效果为 30% 到 40%，最大延迟的降低效果为三倍，但会浪费 10% 到 20% 的带宽，比 TCP 多。它是通过纯算法实现的，不负责底层协议（如 UDP）的发送和接收，需要用户为底层数据包定义自己的传输模式，并以回调的方式提供给 KCP。甚至时钟也需要从外部传入，没有任何内部系统调用。"

我们对 KCP 感到非常兴奋，并强烈建议在您的项目中尝试使用它。=> 以前我们有纯 C# 传输 (transports) 很慢 => 和本地 C 传输 (transports) 很快，但难以维护。

KCP 为我们带来了两全其美的优势：它像我们的本地 C 传输 (transports) 一样快速，同时又易于维护，因为它是 100% 纯 C#！

### 使用方法 <a href="#usage" id="usage"></a>

将 KCP 传输 (Transport) 组件添加到您的 NetworkManager。

解除 UDP（不是 TCP），端口 7777（默认端口，除非您更改它）的阻止。

### 鸣谢 <a href="#credits" id="credits"></a>

skywind3000 原始 kcp.c 的贡献者 ([https://github.com/skywind3000/kcp](https://github.com/skywind3000/kcp)) kcp-csharp 的第一个 C# 移植贡献者 ([https://github.com/limpo1989/kcp-csharp](https://github.com/limpo1989/kcp-csharp)) paul/lymdum/uwee 的连接处理贡献者。

请粘贴您要翻译的 Markdown 内容。
