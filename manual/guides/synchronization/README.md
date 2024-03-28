# 同步

状态同步指的是脚本中属于整数、浮点数、字符串和布尔值等值的同步。

状态同步是从服务器同步到远程客户端的。本地客户端不会序列化数据。它不需要，因为它与服务器共享场景。但是，SyncVar 钩子会在本地客户端上调用。

数据不会在相反方向 - 从远程客户端到服务器进行同步。要做到这一点，您需要使用 Commands。

* [SyncVars](syncvars.md)\
  &#x20;SyncVars 是从服务器同步到客户端的继承自 NetworkBehaviour 的脚本的变量。
* [SyncEvents (已弃用)](syncevent.md)\
  &#x20;SyncEvents 是类似于 ClientRpc 的网络事件，但不是在游戏对象上调用函数，而是触发事件。 **重要：** 在版本 18.0.0 中已移除，请查看此 [Issue](https://github.com/vis2k/Mirror/pull/2178) 获取更多信息。
* [SyncLists](synclists.md)\
  &#x20;SyncLists 包含值列表并将数据从服务器同步到客户端。
* [SyncDictionary](syncdictionary.md)\
  &#x20;SyncDictionary 是包含无序键值对列表的关联数组。
* [SyncHashSet](synchashset.md)\
  &#x20;一个不重复值的无序集合。
* [SyncSortedSet](syncsortedset.md)\
  &#x20;一个不重复值的排序集合。

## 同步至所有者 (Sync To Owner) <a href="#sync-to-owner" id="sync-to-owner"></a>

通常情况下，您可能不希望某些玩家数据对其他玩家可见。在检查器中将“Network Sync Mode”从“Observers”（默认）更改为“Owner”，以便让 Mirror 知道仅与拥有客户端同步数据。

例如，假设您正在制作一个库存系统。假设玩家 A、B 和 C 在同一区域。整个网络中将有 12 个对象：

* 客户端 A 有玩家 A（自己）、玩家 B 和玩家 C
* 客户端 B 有玩家 A、玩家 B（自己）和玩家 C
* 客户端 C 有玩家 A、玩家 B 和玩家 C（自己）
* 服务器有玩家 A、玩家 B、玩家 C

每一个都会有一个 Inventory 组件

假设玩家 A 捡起了一些战利品。服务器会将这些战利品添加到玩家 A 的库存中，这个库存会有一个项目（SyncLists）的列表。

默认情况下，Mirror 现在必须在所有地方同步玩家 A 的库存，这意味着向客户端 A、客户端 B 和客户端 C 发送更新消息，因为他们都有玩家 A 的副本。这是一种浪费，客户端 B 和客户端 C 不需要知道玩家 A 的库存，他们从未在屏幕上看到过。这也是一个安全问题，有人可能会黑客客户端并显示其他人的库存，并利用它获取优势。

如果将 Inventory 组件中的 "Network Sync Mode" 设置为 "Owner"，那么玩家 A 的库存将只与客户端 A 同步。

现在，假设不是 3 个人，而是在一个区域有 50 个人，其中一个人捡起了战利品。这意味着，与其向 50 个不同的客户端发送 50 条消息，你只需要发送 1 条。这在游戏中的带宽上可能会产生很大的影响。

其他典型的用例包括任务、玩家在卡牌游戏中的手牌、技能、经验，或者任何你不需要与其他玩家共享的数据。

## 高级状态同步（Advanced State Synchronization）<a href="#advanced-state-synchronization" id="advanced-state-synchronization"></a>

在大多数情况下，使用 SyncVars 对于游戏脚本将它们的状态序列化到客户端已经足够了。然而，在某些情况下，您可能需要更复杂的序列化代码。本页面仅适用于需要超出 Mirror 正常 SyncVar 功能的自定义同步解决方案的高级开发人员。

## 自定义序列化函数（Custom Serialization Functions）<a href="#custom-serialization-functions" id="custom-serialization-functions"></a>

为了执行您自己的自定义序列化，您可以在 NetworkBehaviour 上实现虚拟函数，用于 SyncVar 序列化。这些函数是：

```csharp
public virtual bool OnSerialize(NetworkWriter writer, bool initialState);
```

```csharp
public virtual void OnDeserialize(NetworkReader reader, bool initialState);
```

使用`initialState`标志来区分游戏对象首次序列化和增量更新时的情况。第一次将游戏对象发送给客户端时，必须包含完整的状态快照，但后续更新可以通过仅包含增量更改来节省带宽。

`OnSerialize`函数应返回true以指示应发送更新。如果返回true，则该脚本的脏位将设置为零。如果返回false，则脏位不会更改。这允许多个脚本更改在一段时间内累积，并在系统准备就绪时发送，而不是每帧发送。

`OnSerialize`函数仅在`initialState`或`NetworkBehaviour`脏时调用。只有当`SyncVar`或`SyncObject`（例如`SyncList`）自上次OnSerialize调用以来发生更改时，`NetworkBehaviour`才会变为脏。在数据发送后，直到下一个`syncInterval`（在检查器中设置）之前，`NetworkBehaviour`将不再是脏的。`NetworkBehaviour`也可以通过手动调用`SetDirtyBit`标记为脏（这不会绕过syncInterval限制）。

虽然这样做是有效的，但通常最好让Mirror生成这些方法并为您的特定字段提供[自定义序列化器](../serialization.md)。

## 序列化流程（Serialization Flow）<a href="#serialization-flow" id="serialization-flow"></a>

附加了Network Identity组件的游戏对象可以具有从`NetworkBehaviour`派生的多个脚本。序列化这些游戏对象的流程如下：

在服务器端：

* 每个`NetworkBehaviour`都有一个脏掩码。此掩码在`OnSerialize`中作为`syncVarDirtyBits`可用
* `NetworkBehaviour`脚本中的每个SyncVar在脏掩码中被分配一个位
* 更改SyncVars的值会导致该SyncVar的位在脏掩码中设置
* 或者，调用`SetDirtyBit`直接写入脏掩码
* NetworkIdentity游戏对象在服务器端作为其更新循环的一部分进行检查
* 如果`NetworkIdentity`上的任何`NetworkBehaviours`是脏的，则为该游戏对象创建一个`UpdateVars`数据包
* `UpdateVars`数据包通过调用游戏对象上每个`NetworkBehaviour`的`OnSerialize`来填充
* 未脏的`NetworkBehaviours`为其脏位在数据包中写入零
* 脏的`NetworkBehaviours`写入其脏掩码，然后是已更改的SyncVars的值
* 如果`OnSerialize`对于`NetworkBehaviour`返回true，则该`NetworkBehaviour`的脏掩码将被重置，因此直到其值更改为止不会再次发送
* `UpdateVars`数据包发送到正在观察游戏对象的准备就绪的客户端

# 在客户端(Client)：

* 接收到一个游戏对象的`UpdateVars packet`(更新变量数据包)
* 对游戏对象上的每个`NetworkBehaviour`(网络行为)脚本都会调用`OnDeserialize`函数
* 游戏对象上的每个`NetworkBehaviour`脚本都会读取一个脏位掩码(dirty mask)
* 如果某个`NetworkBehaviour`的脏位掩码为零，则`OnDeserialize`函数会在不再读取任何内容的情况下返回
* 如果脏位掩码的值为非零，则`OnDeserialize`函数会读取与设置的脏位对应的SyncVars的值
* 如果存在SyncVar hook函数，将使用从流中读取的值调用这些函数。

因此，对于这个脚本：

```csharp
public class data : NetworkBehaviour
{
    [SyncVar(hook = nameof(OnInt1Changed))]
    public int int1 = 66;

    [SyncVar]
    public int int2 = 23487;

    [SyncVar]
    public string MyString = "Example string";

    void OnInt1Changed(int oldValue, int newValue)
    {
        // do something here
    }
}
```

以下示例显示了Mirror为`SerializeSyncVars`(序列化同步变量)函数生成的代码，该函数在`NetworkBehaviour.OnSerialize`内调用：

```csharp
public override bool SerializeSyncVars(NetworkWriter writer, bool initialState)
{
    // Write any SyncVars in base class
    bool written = base.SerializeSyncVars(writer, forceAll);

    if (initialState)
    {
        // The first time a game object is sent to a client, send all the data (and no dirty bits)
        writer.WritePackedUInt32((uint)this.int1);
        writer.WritePackedUInt32((uint)this.int2);
        writer.Write(this.MyString);
        return true;
    }
    else 
    {
        // Writes which SyncVars have changed
        writer.WritePackedUInt64(base.syncVarDirtyBits);

        if ((base.get_syncVarDirtyBits() & 1u) != 0u)
        {
            writer.WritePackedUInt32((uint)this.int1);
            written = true;
        }

        if ((base.get_syncVarDirtyBits() & 2u) != 0u)
        {
            writer.WritePackedUInt32((uint)this.int2);
            written = true;  
        }

        if ((base.get_syncVarDirtyBits() & 4u) != 0u)
        {
            writer.Write(this.MyString);
            written = true;     
        }

        return written;
    }
}
```

以下示例显示了Mirror为`DeserializeSyncVars`(反序列化同步变量)函数生成的代码，该函数在`NetworkBehaviour.OnDeserialize`内调用：

```csharp
public override void DeserializeSyncVars(NetworkReader reader, bool initialState)
{
    // Read any SyncVars in base class
    base.DeserializeSyncVars(reader, initialState);

    if (initialState)
    {
        // The first time a game object is sent to a client, read all the data (and no dirty bits)
        int oldInt1 = this.int1;
        this.int1 = (int)reader.ReadPackedUInt32();
        // if old and new values are not equal, call hook
        if (!base.SyncVarEqual(num, ref this.int1))
        {
            this.OnInt1Changed(num, this.int1);
        }

        this.int2 = (int)reader.ReadPackedUInt32();
        this.MyString = reader.ReadString();
        return;
    }

    int dirtySyncVars = (int)reader.ReadPackedUInt32();
    // is 1st SyncVar dirty
    if ((dirtySyncVars & 1) != 0)
    {
        int oldInt1 = this.int1;
        this.int1 = (int)reader.ReadPackedUInt32();
        // if old and new values are not equal, call hook
        if (!base.SyncVarEqual(num, ref this.int1))
        {
            this.OnInt1Changed(num, this.int1);
        }
    }

    // is 2nd SyncVar dirty
    if ((dirtySyncVars & 2) != 0)
    {
        this.int2 = (int)reader.ReadPackedUInt32();
    }

    // is 3rd SyncVar dirty
    if ((dirtySyncVars & 4) != 0)
    {
        this.MyString = reader.ReadString();
    }
}
```

如果一个`NetworkBehaviour`有一个还具有序列化函数的基类，那么基类函数也应该被调用。

请注意，为了发送给客户端，用于游戏对象状态更新的`UpdateVar`(更新变量)数据包可能会在发送之前被聚合在缓冲区中，因此单个传输层数据包可能包含多个游戏对象的更新。
