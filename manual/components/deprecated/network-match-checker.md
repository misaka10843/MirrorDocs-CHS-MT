---
description: '已弃用：请改用匹配兴趣管理（Match Interest Management）代替。'
---

# 网络匹配检查器（Network Match Checker）

{% hint style="danger" %}
**网络匹配检查器已过时 - 请改用** [**网络匹配（Network Match）**](../../interest-management/match.md) **组件。**
{% endhint %}

网络匹配检查器组件根据匹配 ID 控制网络对象的可见性。

![](<../../../.gitbook/assets/image (29).png>)

任何带有此组件的对象只会对同一匹配中的其他对象可见。

这可用于将玩家隔离到单个游戏服务器实例中的各自匹配中。

创建匹配时，例如生成并存储一个新的匹配 ID（使用 `System.Guid.NewGuid();`），然后通过 `GetComponent<NetworkMatchChecker>().matchId` 将相同的匹配 ID 分配给网络匹配检查器。

Mirror 内置的观察者系统将会将网络对象上的 SyncVar 和 ClientRpc 隔离，仅向具有相同匹配 ID 的客户端发送更新。
