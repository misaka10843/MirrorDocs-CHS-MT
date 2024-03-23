# 网络房间玩家(Network Room Player)

网络房间玩家(Network Room Player)在房间中为[网络房间管理器(Network Room Manager)](network-room-manager.md)存储每个玩家的状态。当使用此组件时，您需要编写一个脚本，允许玩家指示他们已准备好开始游戏，从而设置ReadyToBegin属性。

拥有网络房间玩家组件的游戏对象还必须具有网络身份(Network Identity)组件。当您在游戏对象上创建网络房间玩家组件时，如果该游戏对象尚未具有网络身份组件，Unity也会在该游戏对象上创建一个网络身份组件。

![](<../../.gitbook/assets/image (80).png>)

* **显示房间GUI(Show Room GUI)**\
  启用此选项以在房间中为玩家显示开发人员GUI。此UI仅用于开发便利。默认情况下启用。
* **准备开始(Ready To Begin)**\
  表示玩家已准备好的诊断指示器。
* **索引(Index)**\
  玩家的诊断索引，例如Player 1，Player 2等。
* **网络同步间隔(Network Sync Interval)**\
  从网络房间玩家发送信息到服务器的速率。

## 方法 <a href="#methods" id="methods"></a>

### 客户端虚拟SyncVar钩子方法 <a href="#client-virtual-syncvar-methods" id="client-virtual-syncvar-methods"></a>

```csharp
public virtual void IndexChanged(int oldIndex, int newIndex) { }
public virtual void ReadyStateChanged(bool oldReadyState, bool newReadyState) { }

```

### 客户端虚拟方法 <a href="#client-virtual-methods" id="client-virtual-methods"></a>

```csharp
public virtual void OnClientEnterRoom() { }
public virtual void OnClientExitRoom() { }
[Obsolete("OnClientReady is deprecated, use the SyncVar ReadyStateChanged hook instead.")]
public virtual void OnClientReady(bool readyState) {}
```
