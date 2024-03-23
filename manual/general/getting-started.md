# 入门指南

本文档描述了使用 Mirror 创建多人游戏的步骤。这里描述的过程是一个简化的、更高级的版本，实际游戏的过程并不总是完全像这样，但它提供了一个基本的流程指南。

## 视频教程 <a href="#video-tutorials" id="video-tutorials"></a>

查看这些[精彩视频](../../community-guides/video-tutorials.md)，向您展示如何开始使用 Mirror。

## 脚本模板 <a href="#script-templates" id="script-templates"></a>

* 更快地创建新的网络行为和其他常见脚本

查看[脚本模板](script-templates.md)。

## 网络管理器设置 <a href="#networkmanager-set-up" id="networkmanager-set-up"></a>

* 从[资源 > 创建 > Mirror](script-templates.md)菜单创建一个新的网络管理器。
* 在场景中添加一个新的游戏对象，并将其重命名为“NetworkManager”。
* 将新创建的网络管理器组件添加到“NetworkManager”游戏对象中。
* 将[NetworkManagerHUD](../components/network-manager-hud.md)组件添加到游戏对象中。这提供了管理网络游戏状态的默认 UI。

查看[使用 NetworkManager](../components/network-manager.md)。

## 玩家预制体 <a href="#player-prefab" id="player-prefab"></a>

* 找到游戏中玩家游戏对象的预制体，或从玩家游戏对象创建一个预制体
* 将 NetworkIdentity 组件添加到玩家预制体中
* 在 NetworkManager 的 Spawn Info 部分设置 `Player Prefab` 为玩家预制体
* 如果场景中存在玩家游戏对象实例，则将其移除

查看[玩家对象](../guides/gameobjects/player-gameobjects.md)以获取更多信息。

## 玩家移动 <a href="#player-movement" id="player-movement"></a>

* 将 NetworkTransform 组件添加到玩家预制体中
* 在组件上勾选 Client Authority 复选框。
* 更新输入和控制脚本以尊重 `isLocalPlayer`
* 重写 OnStartLocalPlayer 以控制场景中的主摄像机给玩家使用。

例如，此脚本仅处理本地玩家的输入：

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

## 基本玩家游戏状态 <a href="#basic-player-game-state" id="basic-player-game-state"></a>

* 将包含重要数据的脚本制作为 Network Behaviours（网络行为）而不是 MonoBehaviours（单体行为）
* 将重要成员变量制作为 SyncVars（同步变量）

参见[状态同步](../guides/synchronization/)。

## 远程操作 <a href="#networked-actions" id="networked-actions"></a>

* 将执行重要操作的脚本制作为 Network Behaviours（网络行为）而不是 MonoBehaviours（单体行为）
* 将执行重要玩家操作的更新函数制作为 commands（命令）

参见[远程操作](../guides/communications/remote-actions.md)。

## 非玩家 GameObjects <a href="#non-player-game-objects" id="non-player-game-objects"></a>

修复非玩家预制件，如敌人：

* 添加 NetworkIdentity（网络标识）组件
* 添加 NetworkTransform（网络变换）组件
* 使用 NetworkManager（网络管理器）注册可生成的预制件
* 更新包含游戏状态和操作的脚本

## 生成器 <a href="#spawners" id="spawners"></a>

* 可能将生成器脚本更改为 NetworkBehaviours（网络行为）
* 修改生成器仅在服务器上运行（使用 isServer 属性或 `OnStartServer()` 函数）
* 为创建的游戏对象调用 `NetworkServer.Spawn()`

## 玩家生成位置 <a href="#spawn-positions-for-players" id="spawn-positions-for-players"></a>

* 添加一个新的游戏对象并将其放置在玩家的起始位置
* 在新游戏对象上添加 NetworkStartPosition（网络起始位置）组件
