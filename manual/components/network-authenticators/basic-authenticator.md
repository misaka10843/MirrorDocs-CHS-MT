# 基本认证器（Basic Authenticator）

Mirror 在 Mirror / Authenticators 文件夹中包含一个基本认证器，它只使用简单的用户名和密码。

* 将基本认证器脚本拖放到具有 Network Manager 的场景中对象的检视器中
* 基本认证器组件将自动分配到 Network Manager 中的 Authenticator 字段

完成后，应该如下所示：

<div align="left">

<img src="../../../.gitbook/assets/image (101).png" alt="Network Manager with Basic Authenticator assigned">

</div>

<div align="left">

<img src="../../../.gitbook/assets/image (54).png" alt="Basic Authenticator">

</div>

* 服务器凭据可以在设计时或运行时设置
* 客户端凭据应在运行时设置，例如，从 UI 输入字段中，在调用 `NetworkManager.singleton.StartClient();` **之前**

{% hint style="info" %}
除非您想要订阅自己代码中的事件以供自己使用，否则不需要将任何内容分配给事件列表。Mirror 对这两个事件都有内部监听器。
{% endhint %}
