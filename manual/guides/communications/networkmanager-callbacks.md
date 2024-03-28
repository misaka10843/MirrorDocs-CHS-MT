# 网络管理器回调(NetworkManager Callbacks)

{% hint style="info" %}
参见 [NetworkManager](https://storage.googleapis.com/mirror-api-docs/html/d7/d5a/class\_mirror\_1\_1\_network\_manager.html) 在 API 参考中的页面。
{% endhint %}

在多人游戏的正常运行过程中会发生许多事件，比如主机启动、玩家加入或玩家离开等。每个可能发生的事件都有一个相关的回调函数，您可以在自己的代码中实现以在事件发生时采取行动。

要为 `NetworkManager` 这样做，您需要创建一个继承自 `NetworkManager` 的自定义脚本。然后，您可以重写 `NetworkManager` 上的虚拟方法，用您自己的实现来处理特定事件发生时应该发生的事情。

本页面列出了您可以在 `NetworkManager` 上实现的所有虚拟方法(回调函数)以及它们发生的时机。发生的回调函数及其顺序略有不同，具体取决于游戏运行的模式，因此下面分别列出了每种模式的回调函数。

游戏可以在主机(host)、客户端(client)或仅服务器(server-only)三种模式中运行。每种模式的回调函数如下：

## 主机模式(Host Mode): <a href="#host-mode" id="host-mode"></a>

当主机启动时：

* `OnStartServer` (开始服务器)
* `OnStartHost` (开始主机)
* `OnServerConnect` (服务器连接)
* `OnStartClient` (开始客户端)
* `OnClientConnect` (客户端连接)
* `OnServerSceneChanged` (服务器场景变更)
* `OnServerReady` (服务器准备就绪)
* `OnServerAddPlayer` (服务器添加玩家)
* `OnClientChangeScene` (客户端场景变更)
* `OnClientSceneChanged` (客户端场景变更)

当客户端连接时：

* `OnServerConnect` (服务器连接)
* `OnServerReady` (服务器准备就绪)
* `OnServerAddPlayer` (服务器添加玩家)

当客户端断开连接时：

* `OnServerDisconnect` (服务器断开连接)

当主机停止时：

* `OnStopHost` (停止主机)
* `OnServerDisconnect` (服务器断开连接)
* `OnStopClient` (停止客户端)
* `OnStopServer` (停止服务器)

## 客户端模式(Client Mode) <a href="#client-mode" id="client-mode"></a>

当客户端启动时：

* `OnStartClient` (开始客户端)
* `OnClientConnect` (客户端连接)
* `OnClientChangeScene` (客户端场景变更)
* `OnClientSceneChanged` (客户端场景变更)

当客户端停止时：

* `OnStopClient` (停止客户端)
* `OnClientDisconnect` (客户端断开连接)

## 服务器模式(Server Mode) <a href="#server-mode" id="server-mode"></a>

当服务器启动时：

* `OnStartServer` (在启动服务器时)
* `OnServerSceneChanged` (在服务器场景更改时)

当客户端连接时：

* `OnServerConnect` (在服务器连接时)
* `OnServerReady` (在服务器准备就绪时)
* `OnServerAddPlayer` (在服务器添加玩家时)

当客户端断开连接时：

* `OnServerDisconnect` (在服务器断开连接时)

当服务器停止时：

* `OnStopServer` (在服务器停止时)
