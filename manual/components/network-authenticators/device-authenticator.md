# 设备验证器

设备验证器使用 `SystemInfo.deviceUniqueIdentifier` 来识别客户端。

对于不支持 `deviceUniqueIdentifier` 的平台，会生成一个 GUID 并存储在 `PlayerPrefs` 中。

{% hint style="warning" %}
注意：`deviceUniqueIdentifier` 可以被伪造，因此无法保证安全性。
{% endhint %}

* 将设备验证器脚本拖动到场景中具有网络管理器的对象的检查器中
* 设备验证器组件将自动分配给网络管理器中的验证器字段

完成后，应该如下所示：

<div align="left">

<img src="../../../.gitbook/assets/image (108).png" alt="Network Manager with Device Authenticator assigned">

</div>

<div align="left">

<img src="../../../.gitbook/assets/image (18).png" alt="Device Authenticator">

</div>

{% hint style="info" %}
除非您想要订阅自己代码中的事件以供自己使用，否则无需将任何内容分配给事件列表。Mirror 对两个事件都有内部监听器。
{% endhint %}
