# 时间戳批处理

让我们来了解一下 Mirror 是如何发送消息的。

## 配料

您发送的每条消息都将被批处理，直到帧结束，以最小化带宽和传输调用。 例如，如果您发送了大量 10 字节的消息，那么我们通常可以将其中的 120 个放入一个**MTU**大小的大约 1200 字节的批中。

对于传输，以 1200 字节的块发送消息是非常方便的(参见*[\_MTU*](https://en.wikipedia.org/wiki/Maximum\_transmission\_unit)_)。 大于**MTU**的消息作为单个批发送。 确切地说，Transport 通过`Transport.GetBatchThreshold()`决定 Mirror 所针对的批大小。

Mirror 是双向的。 这意味着客户机和服务器都将它们的消息批处理，并在帧的末尾将它们刷新出来。

{% hint style="success"%}
简而言之，TCP/IP 显著降低了带宽并提高了性能。
联系我们

## 时间戳

对于某些网络组件，准确地知道远程设备发送消息的时间是很有用的。

例如，`NetworkTransform`接收服务器的位置，然后在它们之间进行插值。 为了平滑插值，我们需要准确地重建服务器上发生的事情。 为此，我们需要知道一个对象何时位于服务器上的某个位置。

显而易见的解决方案是每次发送`timestamp`和`position`：

```csharp
[Rpc]
public void RpcPositionUpdate(float timestamp, Vector3 position)
{
    // ...
}
```

事实上，这正是我们新`NetworkTransform`组件的早期版本所做的。

对于上面的代码，我们付出了很大的带宽成本，因为对于每个位置消息，我们还需要包括一个 4 字节`float`(或者更好的是，一个 8 字节`double`以获得更高的精度)。 在大世界同步的时候，带宽会迅速增加。

`NetworkTransform`只是众多组件之一。 其他几个可能也需要时间戳，这将进一步增加带宽。

为了让生活更轻松，Mirror 在每个批处理中包含一个 8 字节的`double`精度`timestamp`。 而不是包括在每一个消息，我们包括它一次\~1200 字节的批次，这是几乎不明显的，当谈到带宽。

对于 Mirror 中的任何消息处理程序，您可以通过`NetworkConnection.remoteTimeStamp`从它到达的批处理中获取时间戳。

- 在**客户端**上，所有对象数据都以消息/批的形式从服务器到达。 因此，在任何给定的时间，您都可以通过`NetworkClient.connection.remoteTimeStamp`服务器何时发送对象的`Rpc`/`OnDeserialize`/`OnMessage`处理程序。
  - 请注意，在客户端上，我们不使用对象的`connectionToServer`，因为只有玩家拥有的对象才有到服务器的连接。 相反，我们使用客户端到服务器的`NetworkClient.connection`，它总是保证在那里。
- 在**服务器**上，只有玩家拥有的对象才能从玩家连接中获取消息。 因此，在任何给定的时间，您都可以在客户端通过`connectionToClient.remoteTimeStamp`发送对象的`Cmd`/`OnDeserialize`/`OnMessage`处理程序时找到 otu。

{% hint style="info"%}
**时间戳批处理**是 Mirror 的独特方法，以通用的世界同步。 \
\
例如，雷神之锤的三角洲快照是 FPS 游戏的理想选择，其中整个世界都适合一个世界状态消息，而不是具有大量实体的大型 MMO 大小的世界。 或者你正在进行一个几乎没有任何世界状态的多人文本冒险，但仍然有很多网络消息。
\
{% endhint %}
**时间戳批处理**非常适合 Mirror 的架构。 它应该可以帮助您减少带宽，无论您从事何种类型的项目。
联系我们
