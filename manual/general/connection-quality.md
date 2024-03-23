# 连接质量

Mirror 在平滑处理较差的网络连接方面做了很多工作。

然而，我们能做的也只有这么多。

{% hint style="warning" %}
对于较差的连接，最佳实践是显示“**连接质量差**”警告。
{% endhint %}

Mirror 的 ConnectionQuality 包括三个部分，使其非常容易应用到你的游戏中。

### **ConnectionQuality.cs**

独立的连接质量枚举和启发式方法，如果需要的话可以在 Unity 之外使用。

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

目前它提供两种启发式方法：&#x20;

* **Simple**（基于 Ping 和 Jitter）&#x20;
* **Pragmatic**（基于快照插值）。

### NetworkPingDisplay

这个组件可以添加到 NetworkManager 中，在屏幕右下角显示 Ping 和连接质量指示器。随意根据需要修改此组件，或者创建你自己的组件。

<figure><img src="../../.gitbook/assets/2023-06-25 - connection quality, gui, callback.png" alt=""><figcaption></figcaption></figure>

### **NetworkManager 回调**

*   **CalculateConnectionQuality()** 可以被重写以注入你自己的启发式方法。默认情况下，它使用上述的 **Simple** 启发式方法。这个方法每隔 **connectionQualityInterval** 秒调用一次，这个时间间隔也可以在 NetworkManager 中配置。



    ```csharp
    protected virtual void CalculateConnectionQuality()
    {
        NetworkClient.connectionQuality = ConnectionQualityHeuristics.Simple(
            NetworkTime.rtt, 
            NetworkTime.rttVar
        );
    }
    ```
*   **OnConnectionQualityChanged**() 可以用于向用户显示警告。默认情况下，它会发出一个日志消息 - 用于调试用户日志等。



    ```csharp
    protected virtual void OnConnectionQualityChanged(ConnectionQuality previous, ConnectionQuality current)
    {
        Debug.Log($"[Mirror] Connection Quality changed from {previous} to {current}");
    }
    ```
