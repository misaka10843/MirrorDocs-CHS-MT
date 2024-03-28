---
description: 距离兴趣管理
---

# 距离(Distance)

## 距离兴趣管理(Distance Interest Management)

对于兴趣管理的直接、蛮力解决方案是简单地将所有实体发送到范围内的所有连接。这就是**网络接近性检查器(Network Proximity Checker)**所做的事情：

```csharp
foreach spawned entity:
    foreach connection:
        if (Vector3.Distance(spawned, connection) < visRange):
            connection.Send(spawned);
```

唯一的缺点是检查每个实体与每个连接相对昂贵。因此，如果您需要大量实体或连接，最好使用像[**空间哈希(Spatial Hashing)**](spatial-hashing.md)这样更快的算法。

### 设置

将**距离兴趣管理(Distance Interest Management)**组件添加到与您的**网络管理器(Network Manager)**相同的对象中：

![](<../../.gitbook/assets/image (122).png>)

**可见范围(Vis Range)**定义了玩家周围的半径，从中接收世界更新。

**重建间隔(Rebuild Interval)**以秒为单位，确定Mirror重新计算对象对客户端的可见性的频率。

### 自定义可见范围

上面的可见范围设置适用于所有网络化对象作为“默认”范围。您可以在需要不同范围的对象上覆盖此值，例如，对于相对于场景中大多数其他物体而言非常大或非常小的异常情况。要做到这一点，只需将**距离兴趣管理自定义范围(Distance Interest Management Custom Range)**组件添加到这些对象上，并根据需要设置这些对象的可见范围值。

![](<../../.gitbook/assets/image (20).png>)
