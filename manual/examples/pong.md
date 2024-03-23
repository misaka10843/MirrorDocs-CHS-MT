# Pong（乒乓球）

一个简单的示例“如何使用 Mirror 构建多人游戏”是 Pong（乒乓球），它包含在 Mirror 的 AssetStore 包中。它展示了 NetworkManager（网络管理器）、NetworkManagerHUD（网络管理器 HUD）、NetworkBehaviour（网络行为）、NetworkIdentity（网络标识）、NetworkTransform（网络变换）、NetworkStartPosition（网络起始位置）和 NetworkingAttributes（网络属性）的用法。

![](<../../.gitbook/assets/image (78).png>)

## 设置玩家数量 <a href="#setting-the-number-of-players" id="setting-the-number-of-players"></a>

首先，让我们看一下主场景中的 NetworkManager（网络管理器）对象。将 NetworkManager 组件添加到游戏对象时，已经设置了一些默认设置（**不在加载时销毁**，**后台运行**，...）。对于玩 Pong 游戏，最大玩家数量为 2，因此设置 **Network Info/Max connections（网络信息/最大连接数）** 也将为 2。由于在此示例中没有其他场景（房间、在线或离线场景），因此 **Offline Scene（离线场景）** 和 **Online Scene（在线场景）** 的属性将保持为空。

## 创建玩家 <a href="#creating-the-player" id="creating-the-player"></a>

此外，每个玩家都需要一个球拍来进行游戏。加入游戏的每个玩家都将拥有自己的可控对象，代表他在游戏中的位置。这个游戏对象称为 _PlayerObject_。为了生成 _PlayerObject_，必须创建一个预制体，其中至少包含一个带有已选中 **Local Player Authority（本地玩家权限）** 的 NetworkIdentity 组件。**Local Player Authority（本地玩家权限）** 允许玩家控制和修改游戏对象的属性（例如移动）。NetworkManager 需要引用此预制体，该预制体位于 **Spawn Info/Player Prefab（生成信息/玩家预制体）** 中。为了使玩家的移动在网络上同步，玩家预制体还包含一个 NetworkTransform。

![](<../../.gitbook/assets/image (110).png>)

## 玩家起始位置 <a href="#player-start-position" id="player-start-position"></a>

主场景包含 2 个仅带有 NetworkStartPosition（网络起始位置）组件的游戏对象（场景中的 RacketSpawnLeft（球拍生成位置左）和 RacketSpawnRight（球拍生成位置右））。这些变换将自动被 NetworkManager 注册为生成位置。

![](<../../.gitbook/assets/image (91).png>)

## 设置网络<a href="#setting-up-the-network" id="setting-up-the-network"></a>

一个非常方便的组件用于建立/测试连接是[Network Manager HUD](../components/network-manager-hud.md)(网络管理器HUD)。它提供了基本功能，可以作为客户端(Client)、服务器(Server)或主机(Host)(同时作为客户端和服务器)启动游戏。它需要Network Manager组件。

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption><p>网络管理器HUD</p></figcaption></figure>

## Pong的球<a href="#the-ball-of-pong" id="the-ball-of-pong"></a>

球是Pong的主要焦点，因为这是需要得分的对象。它的NetworkIdentity(网络身份)组件既没有**Server Only**（仅服务器）也没有**Local Player Authority**（本地玩家权限）被选中，因为它由服务器物理引擎移动，并且可以受到玩家的影响。与_PlayerObject一样，位置通过NetworkTransform进行同步。当有多个场景时，球可以由NetworkManager生成，但为了保持这个示例简单，它直接放置在主场景中。
