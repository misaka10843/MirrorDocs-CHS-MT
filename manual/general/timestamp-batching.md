# 时间戳批处理

让我们学习一下 Mirror 如何发送消息。

## 批处理

每条发送的消息都会被批处理，直到帧结束，以最小化带宽和传输调用。例如，如果您发送了许多 10 字节的消息，那么我们通常可以将大约 120 条消息放入一个大小为 **MTU** 的批次中，大约为 1200 字节。

对于传输来说，在 1200 字节的块中发送消息非常方便 _(参见_ [_MTU_](https://en.wikipedia.org/wiki/Maximum_transmission_unit)_)_。大于 **MTU** 的消息将作为单个批次发送。确切地说，传输通过 `Transport.GetBatchThreshold()` 确定 Mirror 的批处理目标大小。

Mirror 的批处理是双向的。这意味着客户端和服务器都会对其消息进行批处理，并在帧结束时将它们刷新出去。

{% hint style="success" %}
简而言之，批处理显著减少了带宽，并提高了性能。
{% endhint %}

## 时间戳

对于一些网络组件，准确知道远程发送消息的时间是很有用的。

例如，`NetworkTransform` 接收服务器的位置，然后在它们之间进行插值。为了平滑插值，我们需要准确重建服务器上发生的情况。为此，我们需要知道对象何时在服务器上处于某个位置。

显而易见的解决方案是每次都发送 `timestamp` 和 `position`：

```csharp
[Rpc]
public void RpcPositionUpdate(float timestamp, Vector3 position)
{
    // ...
}
```

事实上，这就是我们新的 `NetworkTransform` 组件的早期版本所做的。

对于上述代码，我们需要支付显著的带宽成本，因为对于每个位置消息，我们还需要包含一个 4 字节的 `float`（或者更好的是，一个 8 字节的 `double` 以获得更高的精度）。在同步大型世界时，带宽会迅速累积。

`NetworkTransform` 只是众多组件之一。其他几个组件可能也需要时间戳，这将进一步增加带宽。

为了简化生活，Mirror 在每个批次中包含一个 8 字节的 `double` 精度 `timestamp`。我们不是在每条消息中包含它，而是在每个大约 1200 字节的批次中包含一次，这在带宽方面几乎不可感知。

在 Mirror 中的任何消息处理程序中，您可以通过`NetworkConnection.remoteTimeStamp`从其所在批次获取时间戳。

- 在**客户端**，所有对象数据都是从服务器以消息/批次的形式到达的。因此，在任何给定时间，您都可以通过`NetworkClient.connection.remoteTimeStamp`找出对象的`Rpc`/`OnDeserialize`/`OnMessage`处理程序是由服务器发送的。
  - 请注意，在客户端，我们不使用对象的`connectionToServer`，因为只有玩家拥有的对象才与服务器建立连接。相反，我们使用客户端的`NetworkClient.connection`到服务器，这总是保证存在的。
- 在**服务器端**，只有玩家拥有的对象才会从玩家连接接收消息。因此，在任何给定时间，您可以通过`connectionToClient.remoteTimeStamp`找出对象的`Cmd`/`OnDeserialize`/`OnMessage`处理程序是由客户端发送的。

{% hint style="info" %}
**时间戳批处理**是 Mirror 对于通用世界同步的独特方法。 \

例如，Quake 的 Delta 快照非常适合 FPS 游戏，其中整个世界适合一个世界状态消息，但对于具有大量实体的较大 MMO 大小的世界并不理想。或者您可能正在开发一个几乎没有世界状态但仍有大量网络消息的多人文本冒险游戏。 \

**时间戳批处理**很好地融入了 Mirror 的架构。无论您正在开发什么类型的项目，它都应该帮助您减少带宽使用。
{% endhint %}
