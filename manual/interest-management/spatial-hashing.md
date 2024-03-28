---
description: 空间哈希兴趣管理
---

# 空间哈希(Spatial Hashing) (Spatial Hashing)

## **空间哈希兴趣管理(Spatial Hashing Interest Management)**

首先要明确一点：**"空间哈希(Spatial Hashing)"**听起来很复杂，让我们这些网络程序员可以炫耀一下。实际上，这项技术非常简单，如果你之前使用过uMMORPG，那么你可能还记得它叫做**"Grid Checker"**。不过我们还是使用**"空间哈希(Spatial Hashing)"**，因为这是行业术语。

### 设置

将**空间哈希兴趣管理(Spatial Hashing Interest Management)**组件添加到与您的**网络管理器(Network Manager)**相同的对象中：

![](<../../.gitbook/assets/image (68).png>)

### **工作原理**

以前，我们对每个生成的实体与每个连接进行**Vector3.Distance**检查。现在，我们将每个生成的实体放入一个**网格(Grid)**，对于每个连接，我们将所有**8个相邻**的网格条目发送给它。这是非常快速的。在早期的uMMORPG测试中，它比距离检查快30倍。该算法较少复杂，因此可以很好地扩展到大量实体。

![来源: https://www.dynetisgames.com/2017/04/05/interest-management-mog/](<../../.gitbook/assets/image (27).png>)

{% hint style="info" %}
查看Mirror的**Benchmark**示例。它使用空间哈希(Spatial Hashing)，并显示一个运行时滑块，让您调整可见范围。
{% endhint %}
