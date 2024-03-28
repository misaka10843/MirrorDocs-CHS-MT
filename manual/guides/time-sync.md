# 时间同步(Time Synchronization)

对于许多算法，您需要在客户端和服务器之间同步时钟。Mirror会自动为您完成这一操作。

要获取当前时间，请使用以下代码：

```csharp
double now = NetworkTime.time;
```

它将在客户端和服务器中返回相同的值。当服务器启动时，时间从0开始。请注意，时间是一个double类型，不应该转换为float类型。将其转换为float类型会导致时钟在一段时间后失去精度：

- 经过1天后，准确性降至8毫秒
- 经过10天后，准确性为62毫秒
- 经过30天后，准确性为250毫秒
- 经过60天后，准确性为500毫秒

Mirror还会计算应用程序所见的RTT（往返时间）：

```csharp
double rtt = NetworkTime.rtt;
```

您可以测量准确性。

```csharp
double time_standard_deviation = NetworkTime.timeSd;
```

例如，如果返回值为0.2，则表示时间测量大约上下波动0.2秒。

网络抖动通过使用EMA平滑值来进行补偿。\
您可以配置希望发送ping的频率：

```csharp
NetworkTime.PingFrequency = 2;
```

您还可以配置在计算中使用多少个ping结果：

```csharp
NetworkTime.PingWindowSize = 10;
```
