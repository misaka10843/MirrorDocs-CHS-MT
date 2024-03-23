---
description: '已弃用：请改用团队兴趣管理（Team Interest Management）替代。'
---

# 网络所有者检查器（Network Owner Checker）

{% hint style="danger" %}
**网络所有者检查器已过时 - 请改用** [**网络团队（Network Team）**](../../interest-management/team.md) **组件。**
{% endhint %}

网络所有者检查器组件根据所有者客户端控制网络对象的可见性。

任何带有此组件的对象只对拥有其所有权的客户端可见。

当您想要生成具有客户端所有权的网络对象并且仅对所有者可见时，可以使用此功能，例如交互式 UI 或 NPC。由于客户端可以在拥有权限的对象上调用命令，因此这些对象可以具有包含命令的网络行为，客户端可以直接调用这些命令。
