# 属性 (Attributes)

网络属性被添加到 Network Behaviour 脚本的成员函数中，以便让它们在客户端或服务器上运行。

这些属性可以用于 Unity 游戏循环方法，如 **Start** 或 **Update**，以及其他已实现的方法。

{% hint style="info" %}
**注意**：当使用抽象或虚拟方法时，属性也需要应用到覆盖方法上。
{% endhint %}

* **\[Server]**\
  只有服务器可以调用该方法（在客户端调用时会发出警告）。
* **\[ServerCallback]**\
  与 **Server** 相同，但在客户端调用时不会发出警告。
* **\[Client]**\
  只有客户端可以调用该方法（在服务器调用时会发出警告）。
* **\[ClientCallback]**\
  与 **Client** 相同，但在服务器调用时不会发出警告。
* **\[Command]**\
  从客户端调用此方法在服务器上运行此函数。确保验证输入等。不可能从服务器调用此方法。如果要从服务器调用该方法，可以使用此作为另一个函数的包装。另请参见 [**远程操作**](communications/remote-actions.md) 和 [**数据类型**](data-types.md)。
* **\[ClientRpc]**\
  服务器使用远程过程调用（RPC）在客户端上运行该函数。另请参见：[**远程操作**](communications/remote-actions.md)。
* **\[TargetRpc]**\
  这是一个可以放在 Network Behaviour 类的方法上的属性，允许它们从服务器上调用客户端。与 ClientRpc 属性不同，这些函数在一个单独的目标客户端上调用，而不是所有已准备好的客户端。另请参见：[**远程操作**](communications/remote-actions.md)。
* **\[SyncVar]**\
  [**同步变量**](synchronization/syncvars.md) 用于自动将变量从服务器同步到所有客户端。不要从客户端分配它们，这是没有意义的。不要让它们为空，否则会出错。您可以使用 int、long、float、string、Vector3 等（所有简单类型）以及 Network Identity 和游戏对象（如果游戏对象附有 Network Identity）。您可以使用 [**SyncVar 钩子**](synchronization/syncvar-hooks.md) 在客户端接收来自服务器的更新时运行代码。

请粘贴您要翻译的 Markdown 内容。
