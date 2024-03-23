# 网络管理器(Network Manager)

网络管理器(Network Manager)是用于管理多人游戏的网络方面的组件。它将许多有用的功能集成到一个地方，并使创建、运行和调试多人游戏变得尽可能简单。

网络管理器的功能包括：

- 游戏状态管理
- 生成管理
- 场景管理
- 调试信息
- 自定义

## 使用网络管理器开始 <a href="#getting-started-with-the-network-manager" id="getting-started-with-the-network-manager"></a>

网络管理器(Network Manager)是多人游戏的核心控制组件。要开始，请从Mirror提供的[脚本模板(Script Template)](../general/script-templates.md)中创建新的网络管理器(Network Manager)，在起始场景中创建一个空的游戏对象，并添加新创建的网络管理器(Network Manager)组件。新添加的网络管理器(Network Manager)组件如下所示：

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

在编辑器中的网络管理器(Network Manager)的检视面板允许您配置和控制与网络相关的许多内容。

**注意**: 每个场景中只能有一个活动的网络管理器(Network Manager)，因为它是一个单例。不要将网络管理器(Network Manager)组件放在网络游戏对象(具有网络身份(Network Identity)组件的对象)上，因为Mirror在场景加载时会禁用这些组件。

## 传输(Transports) <a href="#transports" id="transports"></a>

Mirror使用一个单独的组件(从传输(Transport)类派生)来在网络上进行连接。新的默认传输(Transport)是KCP(使用UDP，而不是TCP)。将传输(Transport)分离为自己的组件的设计选择允许游戏开发人员选择最适合其游戏需求的传输(Transport)。更改传输(Transport)就像在网络管理器(Network Manager)对象上交换组件并将其分配给传输(Transport)字段一样简单。

传输(Transports)可用于TCP、UDP、WebGL、Steam等。此外，还有一个多路传输(Multiplex transport)，允许在服务器上同时使用两种传输(Transport)，例如Telepathy和WebSockets，以便桌面和浏览器玩家可以在同一服务器上无缝地玩在一起。有关更多信息，请参阅[传输(Transports)](../transports/)。

## 游戏状态管理 <a href="#game-state-management" id="game-state-management"></a>

一个网络多人游戏可以以三种模式运行 - 作为客户端、作为专用服务器，或者作为同时兼具客户端和服务器功能的主机。

如果你正在使用[网络管理器 HUD](network-manager-hud.md)，它会根据玩家点击的按钮自动告诉网络管理器要以哪种模式启动。如果你正在编写自己的 UI 允许玩家开始游戏，你需要从你自己的代码中调用这些方法。这些方法包括：

* StartClient（开始客户端）
* StartServer（开始服务器）
* StartHost（开始主机）

无论游戏以哪种模式开始（客户端、服务器或主机），都会使用网络地址和传输配置。

* 在客户端模式下，游戏会尝试连接到指定的网络地址。也可以使用完全合格的域名（FQDN）作为网络地址，例如 "game.example.com"。
* 在服务器或主机模式下，游戏会在 `localhost` 上监听传入的连接，其中包括服务器机器的本地网络 IP 地址。

## 生成管理 <a href="#spawn-management" id="spawn-management"></a>

使用网络管理器来管理从预制体实例化的网络游戏对象的生成。

![](<../../.gitbook/assets/image (119).png>)

大多数游戏都有一个代表玩家的预制体，因此网络管理器有一个玩家预制体插槽。你应该将这个插槽分配给你的玩家预制体。当你设置了一个玩家预制体后，游戏会自动从该预制体为每个游戏用户生成一个玩家游戏对象。这适用于托管服务器上的本地玩家以及远程客户端上的远程玩家。在分配给该字段之前，你必须将一个网络标识组件附加到玩家预制体上。

一旦你分配了一个玩家预制体，你可以以主机身份启动游戏并看到玩家游戏对象生成。停止游戏会销毁玩家游戏对象。如果你构建并运行另一个游戏副本，并将其连接为客户端到 _localhost_，网络管理器会使另一个玩家游戏对象出现。当你停止该客户端时，它会销毁该玩家的游戏对象。

除了玩家预制体之外，您还必须在网络管理器中注册其他希望在游戏过程中动态生成的预制体。

您可以将预制体添加到检查器中标记为“Registered Spawnable Prefabs”的列表中。您也可以通过代码注册预制体，使用`NetworkClient.RegisterPrefab`方法。

如果您有一个通过“不要在加载时销毁”（DDOL）持续存在于场景中的网络管理器，您需要将所有可能在任何场景中生成的预制体注册到其中。如果每个场景中都有一个单独的网络管理器，您只需要注册与该场景相关的预制体。

## 起始位置 <a href="#start-positions" id="start-positions"></a>

网络管理器默认会在其定义的变换位置和旋转处生成玩家预制体，但是“Player Spawn Method”属性允许您控制如何选择起始位置，与[网络起始位置](network-start-position.md)组件一起使用。

* 选择随机以在随机选择的起始位置选项处生成玩家。
* 选择循环以在设置列表中循环遍历起始位置选项。

如果随机或循环模式不适合您的游戏，您可以通过代码自定义如何选择起始位置。您可以通过列表`NetworkManager.startPositions`访问可用的网络起始位置组件，并且您可以在网络管理器上使用辅助方法`GetStartPosition`，该方法可在`OnServerAddPlayer`的实现中用于查找起始位置。

## 场景管理 <a href="#scene-management" id="scene-management"></a>

大多数游戏都有多个场景。至少，通常会有一个标题屏幕或起始菜单场景，以及实际进行游戏的场景。网络管理器旨在自动管理场景状态和场景转换，以适用于多人游戏。

网络管理器检查器上有两个场景插槽：离线场景和在线场景。将场景资源拖放到这些插槽中会激活网络场景管理。

当服务器或主机启动时，将加载在线场景（Online Scene）。然后该场景成为当前的网络场景。连接到该服务器的任何客户端都会被指示加载该场景。该场景的名称存储在 `networkSceneName` 属性中。

当网络停止时，通过停止服务器或主机或客户端断开连接，将加载离线场景（Offline Scene）。这使得游戏在从多人游戏断开连接时自动返回到菜单场景。

您还可以在游戏运行时通过调用 `ServerChangeScene` 来更改场景。这会使所有当前连接的客户端也更改场景，并更新 `networkSceneName`，以便新客户端也加载新场景。

在网络场景管理激活时，对游戏状态管理函数（如 `StartHost` 或 `StopClient`）的任何调用都可能导致场景更改。这适用于运行时控制 UI。通过设置场景并调用这些方法，您可以控制多人游戏的流程。

请注意，场景更改会导致前一个场景中的所有游戏对象被销毁。

通常应确保网络管理器在场景之间保持持久，否则在场景更改时网络连接会中断。为此，请确保检查检视面板中的“Don’t Destroy On Load” 复选框。

## 自定义 <a href="#customization" id="customization"></a>

`NetworkManager` 类上有一些虚函数，您可以通过创建一个从 `NetworkManager` 继承的派生类来自定义这些函数。在实现这些函数时，请确保处理默认实现提供的功能。例如，在 `OnServerAddPlayer` 中，必须调用函数 `NetworkServer.AddPlayer` 来激活连接的玩家游戏对象。

为了使这更容易，我们提供了一组[脚本模板](../general/script-templates.md)，其中为您提供了所有覆盖的存根。
