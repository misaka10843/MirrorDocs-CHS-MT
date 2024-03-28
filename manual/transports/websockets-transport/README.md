# WebSockets Transport(# WebSockets Transport)

一个使用[websocket protocol(WebSocket协议)](https://en.wikipedia.org/wiki/WebSocket)的Mirror传输，从而使得在Unity WebGL构建中可以在浏览器中进行多人游戏！

![Simple Web Transport Inspector](../../../.gitbook/assets/simple-web-transport-current-inspector-view.png)

## SSL Settings <a href="#ssl-settings" id="ssl-settings"></a>

SSL设置已经过时，可能会在未来的版本中移除。

{% hint style="info" %}
注意：使用反向代理可以让WebGL性能更好，通常比使用cert.json和PFX文件更容易设置和维护。

\
查看[反向代理](reverse-proxy/)页面获取指令。
{% endhint %}

出于传统目的，您可以在这个[SSL指南](ssl.md)中了解有关简单Web传输和SSL的更多信息。

## Server Settings <a href="#server-settings" id="server-settings"></a>

指定基于websocket的游戏服务器监听的端口。

## Client Settings <a href="#client-settings" id="client-settings"></a>

这些设置允许用户自定义Web游戏客户端的行为。

例如，您可能希望引入一个反向代理服务器，并且您希望配置Web游戏客户端连接到反向代理服务器而不是直接连接到游戏服务器。这些设置使您能够实现这一点。

## Logging <a href="#logging" id="logging"></a>

支持的日志级别：

* `None`
* `Error`
* `Warn` (默认)
* `Info`
* `Verbose`
* `Flood`

可以使用传输中的下拉菜单或直接在代码中设置`Mirror.SimpleWeb.Log.level`来设置日志级别。

为了调试目的，建议将日志级别设置为`Verbose`（或在极端情况下设置为`Flood`）。对于正常使用，建议使用`Info`或`Warn`。

将日志级别设置为`None`将完全禁用日志记录。请谨慎使用此选项。

{% hint style="info" %}
传输在其`Awake`和`OnValidate`方法中应用下拉菜单的值。
{% endhint %}

#### Log methods <a href="#log-methods" id="log-methods"></a>

此传输中的日志方法使用[ConditionalAttribute](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.conditionalattribute?view=netstandard-2.0)，因此根据预处理器定义，它们会被移除。

这个预处理符号影响日志记录：

* `DEBUG` 允许警告/错误日志。

查看[Unity文档](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html)了解如何设置自定义预处理定义。
