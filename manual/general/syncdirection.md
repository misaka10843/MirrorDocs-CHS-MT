# 同步方向

![](<../../.gitbook/assets/2022-10-18 - SyncDirection.png>)

我们最近添加了新的**SyncDirection**功能，因此这里是一个快速概述。

#### 为什么选择 SyncDirection

通常，数据在 OnSerialize 中从服务器同步到客户端。

但是，某些组件(如 NetworkTransform)需要能够在客户端权限移动时从客户端同步到服务器。

以前，我们必须使用\[Command]s 来将客户端权威 NetworkTransform 数据同步到服务器，因为 OnSerialize 只会从服务器到客户端。 这有几个缺点：

- 同时进行 OnSerialize 和手动远程调用需要很多额外的代码
- 这是额外的带宽：每个命令包括一个函数散列，等等.
- Interval 需要手动实现，因为 syncInterval 仅适用于 OnSerialize。

正如您所看到的，如果 OnSerialize 也有一个从客户端到服务器的选项，那就太好了。&# x20;

{% hint style="info"%}
请注意，ClientToServer 有点简化。 从技术上讲，它从所有者客户端到服务器，然后在服务器上广播到所有其他不是所有者的观察者客户端。x20;

_在客户端授权 NetworkTransform 的情况下，这意味着您的本地移动被发送到服务器，然后服务器将其广播给其他玩家，以便他们也看到您的移动。_
联系我们

长话短说，我们已经完全实现了客户端权限组件的 SyncDirection。

总结一下：

- **ServerToClient**是默认值。 **OnSerialize**(以及所有 SyncVar、SyncLists)在每个 syncInterval 从服务器发送到客户端。 在客户端上调用**Oncurialize**。
- **ClientToServer**用于客户端授权组件。 **OnSerialize 在**每个**syncInterval**在所有者客户端上被调用。 然后将其发送到服务器，在那里调用**Oncurialize**。 然后，服务器向除所有者之外的所有其他客户端广播。 然后在其他客户端上调用**Oncurialize**。

请注意，ClientToServer 数据仍然可以在服务器上的 Onticalize 中进行验证。 这就是为什么它在技术上不是"客户端权限"，因此命名为 SyncDirection。 例如，虽然所有者客户端可以将 NetworkTransform 数据同步到服务器，但您仍然可以在应用和广播之前验证服务器的 Onticalize 中的每个移动。
