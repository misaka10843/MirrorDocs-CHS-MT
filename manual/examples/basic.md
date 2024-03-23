# 基础

基础示例演示了如何使用本地实例化的 `PlayerUI` 预制件以及 [SyncVars(同步变量)](../guides/synchronization/syncvars.md) 和事件来管理玩家对象的 UI 对象。

<div align="left">

<img src="../../.gitbook/assets/image (2).png" alt="">

</div>

场景中的 Canvas 拥有一个 `CanvasUI` 脚本，其中包含对其子对象的引用：

<div align="left">

<img src="../../.gitbook/assets/image (4).png" alt="场景 Canvas">

</div>

`PlayerUI` 预制件是一个 UI 片段，具有一个 `PlayerUI` 脚本，其中包含对自身子对象的引用：

<div align="left">

<img src="../../.gitbook/assets/image (36).png" alt="PlayerUI 预制件">

</div>

玩家对象上的 Player 脚本引用了 `PlayerUI` 预制件和三个 `SyncVars(同步变量)`：

<div align="left">

<img src="../../.gitbook/assets/image (19).png" alt="Player 对象">

</div>

Player 脚本还包含三个事件，这些事件由 [SyncVar hooks(同步变量钩子)](../guides/synchronization/syncvar-hooks.md) 调用：

```csharp
public event System.Action<int> OnPlayerNumberChanged;
public event System.Action<Color32> OnPlayerColorChanged;
public event System.Action<int> OnPlayerDataChanged;
```

当玩家对象在客户端上生成时，通过 `CanvasUI` 脚本中提供的引用，在 Canvas 的 `PlayersPanel` 的子对象中实例化一个 `PlayerUI`，并使用相应的 Player 脚本引用调用 `SetPlayer` 方法。`PlayerUI` 脚本订阅上述事件，并在从服务器更新 `SyncVars` 时更新其 UI 元素。
