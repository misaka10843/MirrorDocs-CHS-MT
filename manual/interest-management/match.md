---
描述: 匹配兴趣管理

# Match (Match)

## 匹配兴趣管理

{% hint style="danger" %}
不要用于基于物理的游戏...而是使用[**场景兴趣管理**](scene.md)。
{% endhint %}

匹配兴趣管理适用于非物理游戏，如卡牌、棋盘、街机游戏。

### 设置

将**Match Interest Management**组件添加到与您的**Network Manager(网络管理器)**相同的对象上：

![](<../../.gitbook/assets/image (45).png>)

并将**Network Match(网络匹配)**组件添加到所有网络对象上，包括将参与匹配游戏的玩家预制件。

![](<../../.gitbook/assets/image (22).png>)

在运行时，将相同的`matchId`分配给属于同一匹配的玩家和对象。

查看Mirror附带的**多个匹配**示例，以供参考和灵感。
