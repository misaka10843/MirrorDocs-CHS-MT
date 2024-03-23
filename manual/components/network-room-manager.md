# 网络房间管理器

**请查看 Mirror 文件夹中的 Examples 文件夹中的 Room 示例。**

网络房间管理器是一种专门的[网络管理器(Network Manager)](network-manager.md)，在进入游戏的主要场景之前提供一个多人游戏房间。它允许您设置一个网络，包括：

- 最大玩家限制
- 当所有玩家准备就绪时自动开始
- 游戏开始后锁定，防止晚加入者
- 玩家在房间中选择选项的可定制方式

网络房间管理器有两种类型的玩家对象：

**房间玩家预制体(Room Player Prefab)**

- 每个玩家一个
- 当客户端连接或玩家添加时创建
- 持续存在直到客户端断开连接
- 包含准备标志和配置数据
- 处理房间中的命令
- 必须使用[网络房间玩家(Network Room Player)](network-room-player.md)组件

**玩家预制体(Player Prefab)**

- 每个玩家一个
- 当游戏场景启动时创建
- 离开游戏场景时销毁
- 处理游戏中的命令

![](<../../.gitbook/assets/image (99).png>)

## 属性 <a href="#properties" id="properties"></a>

- **显示房间 GUI**\
  显示房间的默认 OnGUI 控件。
- **最小玩家数**\
  启动游戏所需的最小玩家数量。
- **房间玩家预制体**\
  玩家进入房间时创建的预制体(需要网络房间玩家组件)。
- **房间场景**\
  用于房间的场景。
- **游戏场景**\
  用于主游戏玩法的场景。
- **pendingPlayers**\
  包含准备开始游戏的玩家的列表。
- **roomSlots**\
  管理房间中连接的客户端的插槽的列表。
- **allPlayersReady**\
  布尔值，指示所有玩家是否准备开始游戏。当玩家调用 `CmdChangeReadyState` 指示 true 或 false 时，此值会更改，并在新客户端连接时设置为 false。

## 方法 <a href="#methods" id="methods"></a>

### 服务器虚拟方法 <a href="#server-virtual-methods" id="server-virtual-methods"></a>

```csharp
public virtual void OnRoomStartHost() { }
public virtual void OnRoomStopHost() { }
public virtual void OnRoomStartServer() { }
public virtual void OnRoomStopServer() { }
public virtual void OnRoomServerConnect(NetworkConnectionToClient conn) { }
public virtual void OnRoomServerDisconnect(NetworkConnectionToClient conn) { }
public virtual void OnRoomServerSceneChanged(string sceneName) { }
public virtual GameObject OnRoomServerCreateRoomPlayer(NetworkConnectionToClient conn) { return null; }
public virtual GameObject OnRoomServerCreateGamePlayer(NetworkConnectionToClient conn, GameObject roomPlayer) { return null; }
public virtual void OnRoomServerAddPlayer(NetworkConnectionToClient conn) { }
public virtual bool OnRoomServerSceneLoadedForPlayer(NetworkConnectionToClient conn, GameObject roomPlayer, GameObject gamePlayer) { return true; }
public virtual void OnRoomServerPlayersReady() { }
public virtual void OnRoomServerPlayersNotReady() { }
public virtual void ReadyStatusChanged() { }
```

### 客户端虚拟方法 <a href="#client-virtual-methods" id="client-virtual-methods"></a>

```csharp
public virtual void OnRoomClientEnter() { }
public virtual void OnRoomClientExit() { }
public virtual void OnRoomClientConnect() { }
public virtual void OnRoomClientDisconnect() { }
public virtual void OnRoomStartClient() { }
public virtual void OnRoomStopClient() { }
public virtual void OnRoomClientSceneChanged() { }
[Obsolete("OnRoomClientReady is obsolete, use OnRoomClientDisconnect or OnRoomStopClient.")]
public virtual void OnRoomClientAddPlayerFailed() {}
public virtual void OnGUI() { }
```
