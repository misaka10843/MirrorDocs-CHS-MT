# 网络认证器(Network Authenticators)

当你有一个多人游戏时，通常需要存储关于玩家的信息以供以后的游戏使用，保持游戏统计数据或与朋友进行通信。对于所有这些用例，你通常需要一种唯一标识用户的方式。能够区分用户被称为认证(authentication)。有几种可用的方法，一些示例包括：

* 要求用户提供用户名和密码
* 使用第三方的OAuth2或OpenID身份提供者，如Facebook、Twitter、Google
* 使用第三方服务，如PlayFab、GameLift或Steam
* 使用设备ID，在移动设备中非常流行的方法
* 在Android中使用Google Play
* 在IOS中使用Game Center
* 在你的网站中使用Web服务

## 加密通知 <a href="#encryption-notice" id="encryption-notice"></a>

少数可用的传输方式支持加密，因此如果你想通过Mirror进行认证，我们强烈建议你使用支持加密的传输方式，例如[SimpleWebSocket](https://mirror-networking.gitbook.io/docs/transports/websockets-transport)传输，或者使用UnityWebRequest与通过HTTPS连接的服务进行敏感数据的交换。这可以在认证器内部完成，或在调用`StartClient`之前完成。

## 离线/在线场景

认证发生在离线场景中，并且在预连接阶段与游戏服务器交换消息，因此客户端会一直停留在离线场景，直到认证完成。

## 持久化数据

`NetworkConnection`有一个类型为`Object`的**`authenticationData`**属性，可以设置为几乎任何你需要的东西，比如账户ID、令牌、角色选择等，在认证过程中在服务器和/或客户端上，包括数据的结构，在Mirror的任何其他地方都可以使用客户端的`NetworkConnection`...只需将其强制转换回你放入其中的任何类型。

## 内置认证器 <a href="#basic-authenticator" id="basic-authenticator"></a>

* [基本认证器](basic-authenticator.md)\
  Mirror在Mirror / Authenticators文件夹中包含一个基本认证器，它只使用简单的用户名和密码。
*   [设备认证器](device-authenticator.md)

    在支持的平台上使用 `SystemInfo.deviceUniqueIdentifier`，在不支持的平台上使用 PlayerPrefs 中的 GUID 作为备用方案。

## 自定义认证器 <a href="#custom-authenticators" id="custom-authenticators"></a>

认证器派生自一个抽象类 `Authenticator`，允许您实现任何您需要的认证方案。

从 Assets 菜单中，点击 Create > Mirror > Network Authenticator 来创建您自己的自定义认证器，从我们的[脚本模板](../../general/script-templates.md)中填写消息和验证代码以满足您的需求。当客户端成功认证时，在服务器上调用 `ServerAccept(conn)`，在客户端上调用 `ClientAccept()`。Mirror 正在监听这些事件以继续连接序列。如果您希望在认证后执行其他步骤，请订阅 `OnServerAuthenticated` 和 `OnClientAuthenticated` 事件。

## 消息注册 <a href="#message-registration" id="message-registration"></a>

默认情况下，所有注册到 `NetworkServer` 和 `NetworkClient` 的消息都需要认证，除非另有明确说明。要注册绕过认证的消息，您需要为 `RegisterMessage` 方法的一个布尔参数指定 `false`：

```csharp
NetworkServer.RegisterHandler(OnAuthRequestMessage, false);
```

某些内部消息已经设置为绕过认证：

* 服务器端
  * `NetworkPingMessage`
* 客户端
  * `SceneMessage`
  * `NetworkPongMessage`

## 提示

* 在 `OnStartServer` 和 `OnStartClient` 中为消息注册处理程序。它们分别从 StartServer/StartHost 和 StartClient 调用。
* 如果认证失败，请向客户端发送一条消息，特别是如果有一些问题他们可以解决。
* 当认证失败时，在服务器端和客户端调用 `NetworkConnection` 的 `Disconnect()` 方法。如果您想让用户有几次机会输入正确的凭据，当然可以，但 Mirror 不会为您执行断开连接。
  * 如果您发送一个失败消息，请记得在服务器端的 Disconnect 调用上放置一个小延迟，以便在连接断开之前有机会传递。至少要延迟一帧。

现在你已经有了一个自定义 Authenticator 组件的基础，剩下的就取决于你了。在批准客户端之前，你可以在服务器和客户端之间交换任意数量的自定义消息，以完成你的认证过程。

认证也可以扩展到角色选择和自定义，只需制作额外的消息并在完成认证过程之前与客户端交换它们。这意味着这个过程发生在客户端玩家实际进入游戏或切换到在线场景之前。

如果你编写了一个好的认证器，请考虑与其他用户分享或捐赠给 Mirror 项目。
