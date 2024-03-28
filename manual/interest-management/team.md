---
描述: 团队兴趣管理
---

# Team(团队)

## Team Interest Management(团队兴趣管理)

Team Interest Management(团队兴趣管理)提供了仅对同一团队的玩家可见的网络对象，使用玩家对象上的 Network Team(网络团队) 组件以及需要将可见性限制为特定团队的网络对象。

### 设置

将**Team Interest Management**(团队兴趣管理)组件添加到与您的**Network Manager**(网络管理器)相同的对象上：

![](<../../.gitbook/assets/image (58).png>)

将**Network Team**(网络团队)组件添加到玩家预制件和任何其他属于团队的预制件上。在运行时为属于同一团队的玩家/对象设置相同的 Team ID(团队标识) 值。

![](<../../.gitbook/assets/image (35).png>)

在玩家对象上启用**Force Shown**(强制显示)，以便其他团队的玩家可以看到它们。

### 仅所有者可见

通过为每个玩家分配唯一的 Team ID(团队标识) 值，并仅让客户端看到相应的对象，您可以实现类似于以前的 Network Owner Checker 的“Owner Interest Management”(所有者兴趣管理)。&#x20;
