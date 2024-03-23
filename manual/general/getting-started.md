# 入门

本文档描述了使用 Mirror 创建多人游戏的步骤。 这里描述的过程是一个简化的，更高层次的版本的实际过程，为一个真正的游戏;它并不总是完全像这样工作，但它提供了一个基本的配方的过程。

## 视频教程<a href="#video-tutorials" id="video-tutorials"></a>

看看这些[精彩的视频](../../community-guides/video-tutorials.md),告诉你如何开始使用 Mirror。

## 脚本模板<a href="#script-templates" id="script-templates"></a>

- 更快地创建新的网络行为和其他常见脚本

请参见[脚本模板](script-templates.md)。

## 网络管理器设置<a href="#networkmanager-set-up" id="networkmanager-set-up"></a>

- 从[资源>创建>Mirror](script-templates.md)菜单创建一个新的网络管理器。
- 添加一个新的游戏对象到场景中，并将其重命名为"NetworkManager"。
- 将新创建的网络管理器组件添加到"NetworkManager"游戏对象。
- 将[NetworkManagerHUD](../components/network-manager-hud.md)组件添加到游戏对象。 这提供了用于管理网络游戏状态的默认 UI。

请参阅[使用 NetworkManager](../components/network-manager.md)。

## 播放器预置<a href="#player-prefab" id="player-prefab"></a>

- 在游戏中找到玩家游戏对象的预置，或从玩家游戏对象创建预置
- 将 NetworkIdentity 组件添加到播放器预置
- 将 NetworkManager 的 Spawn Info 部分中的`Player Prefab`设置为 Player 预置
- 从场景中移除玩家游戏对象实例(如果它存在于场景中

有关详细信息，请参见[播放器对象](../guides/gameobjects/player-gameobjects.md)。

## 玩家移动<a href="#player-movement" id="player-movement"></a>

- 将 NetworkTransform 组件添加到播放器预置
- 选中组件上的客户端权限复选框。
- 更新输入和控制脚本以支持`isLocalPlayer`
- OnStartLocalPlayer 为玩家控制场景中的主摄像机。

例如，此脚本仅处理本地播放器的输入：

```csharp
using UnityEngine;
using Mirror;

public class Controls : NetworkBehaviour
{
    void Update()
    {
        // exit from update if this is not the local player
        if (!isLocalPlayer) return;

        // handle player input for movement
    }
}
```

## 基本玩家游戏状态<a href="#basic-player-game-state" id="basic-player-game-state"></a>

- 将包含重要数据的脚本放入 Network Behaviours 而不是 MonoBehaviours
- 将重要的成员变量设置为 SyncVars

请参见[状态同步](../guides/synchronization/)。

## 远程动作<a href="#networked-actions" id="networked-actions"></a>

- 将执行重要操作的脚本设置为 Network Behaviours 而不是 MonoBehaviours
- 将执行重要玩家操作的函数更新为命令

请参见[远程操作](../guides/communications/remote-actions.md)。

## 非玩家游戏对象<a href="#non-player-game-objects" id="non-player-game-objects"></a>

修复非玩家的预设，如敌人：

- 添加 NetworkIdentity 组件
- 添加 NetworkTransform 组件
- 使用 NetworkManager 注册可生成的 Prefabs
- 用游戏状态和动作更新脚本

## 生成器(spawner)<a href="#spawners" id="spawners"></a>

- 可能将 spawner 脚本更改为 NetworkBehaviours
- 修改派生程序使其仅在服务器上运行(使用 isServer 属性或`OnStartServer()`函数)
- 调用`NetworkServer.Spawn()`获取创建的游戏对象

## 玩家的生成位置<a href="#spawn-positions-for-players" id="spawn-positions-for-players"></a>

- 添加一个新的游戏对象并将其放置在玩家的起始位置
- 将 NetworkStartPosition 组件添加到新游戏对象
