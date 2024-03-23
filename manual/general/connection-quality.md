# 连接质量

Mirror 做了很多工作来平滑糟糕的网络连接。

然而，我们能做的只有这么多。

{% hint style="warning"%}
对于连接不良，最好显示"**连接不良**"警告。
联系我们

镜子的连接质量由三个部分组成，使这非常容易为您的游戏。

### **ConnectionQuality.cs**

独立的连接质量枚举和索引，如果需要，可以在 Unity 之外使用。

Mirror 提供以下连接质量级别：

```csharp
public enum ConnectionQuality : byte
{
    EXCELLENT,  // ideal experience for high level competitors
    GOOD,       // very playable for everyone but high level competitors
    FAIR,       // very noticeable latency, not very enjoyable anymore
    POOR,       // unplayable
    ESTIMATING, // still estimating
}
```

目前，它提供两种解决方案：&#x20;

- **简单**(基于 Ping 和 Jitter)&#x20;
- **实用**(基于快照插值)。

### 网站地图

此组件可以添加到 NetworkManager 中，以在屏幕右下方显示 Ping & Connection Quality 指示器。 您可以根据自己的需要随意修改，或者创建自己的。

<figure><img src="../../.gitbook/assets/2023-06-25 - connection quality, gui, callback.png" alt=""> <figcaption></figcaption></figure>

### **NetworkManager 回调**

- **CalculateConnectionQuality()**可以被覆盖以注入您自己的启发式算法。 默认情况下，它使用上面的**简单**启发式。 这被称为 every**connectionQualityInterval** seconds，也可以在 NetworkManager 中配置。

  ```csharp
  protected virtual void CalculateConnectionQuality()
  {
      NetworkClient.connectionQuality = ConnectionQualityHeuristics.Simple(
          NetworkTime.rtt,
          NetworkTime.rttVar
      );
  }
  ```

- **OnConnectionQualityChanged**()可用于向用户显示警告。 默认情况下，这会发出一条日志消息--这对于调试用户日志等很有用。

  ```csharp
  protected virtual void OnConnectionQualityChanged(ConnectionQuality previous, ConnectionQuality current)
  {
      Debug.Log($"[Mirror] Connection Quality changed from {previous} to {current}");
  }
  ```
