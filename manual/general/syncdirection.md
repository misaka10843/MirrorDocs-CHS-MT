# SyncDirection

![](<../../.gitbook/assets/2022-10-18 - SyncDirection.png>)

我们最近添加了新的**SyncDirection（同步方向）**功能，因此这里是一个快速概述。

#### 为什么需要 SyncDirection

通常，在`OnSerialize`中从服务器同步数据到客户端。

然而，一些组件，比如`NetworkTransform（网络变换）`，需要能够从客户端同步到服务器，以便在客户端权限移动的情况下。

以前，我们必须使用`[Command]`来将客户端授权的`NetworkTransform`数据同步到服务器，因为`OnSerialize`只会从服务器到客户端。这有几个缺点：

- 需要编写大量额外代码来同时处理`OnSerialize`和手动远程调用
- 需要额外的带宽：每个`Command`都包括一个函数哈希等
- 需要手动实现间隔，因为`syncInterval`仅适用于`OnSerialize`

正如你所看到的，如果`OnSerialize`也能从客户端到服务器，那将会很好。

{% hint style="info" %}
请注意，`ClientToServer（客户端到服务器）`有点简化。从技术上讲，它是从所有者客户端发送到服务器，然后广播到所有其他不是所有者的观察者客户端。

_在客户端授权的`NetworkTransform`情况下，这意味着您的本地移动被发送到服务器，然后服务器将其广播给其他玩家，以便他们也能看到您的移动。_
{% endhint %}

长话短说，我们已经完全实现了用于客户端权限组件的`SyncDirection`。

总结一下：

- **ServerToClient（服务器到客户端）** 是默认设置。`OnSerialize`（以及所有`SyncVars（同步变量）`，`SyncLists（同步列表）`）每个`syncInterval`从服务器发送到客户端。在客户端上调用`OnDeserialize`。
- **ClientToServer（客户端到服务器）** 用于客户端授权组件。`OnSerialize`在所有者客户端每个`syncInterval`调用一次。然后发送到服务器，在那里调用`OnDeserialize`。然后服务器广播给除所有者之外的所有其他客户端。然后在这些其他客户端上调用`OnDeserialize`。

请注意，`ClientToServer`数据仍然可以在服务器的`OnDeserialize`中进行验证。这就是为什么从技术上讲它不是'客户端权限'，因此称为`SyncDirection`。例如，虽然所有者客户端可能会将`NetworkTransform`数据同步到服务器，但您仍然可以在服务器的`OnDeserialize`中验证每次移动，然后应用并广播它。
