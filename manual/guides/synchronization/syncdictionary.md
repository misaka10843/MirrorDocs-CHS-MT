# SyncDictionary(同步字典)

SyncDictionary(同步字典)是一个包含无序键值对列表的关联数组。键和值可以是任何[支持的 Mirror 类型](../data-types.md)。默认情况下，我们使用 .Net [Dictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=netcore-3.1)，这可能会对键和值施加额外的约束。

SyncDictionary(同步字典)的工作方式类似于[SyncLists(同步列表)](synclists.md)：当您在服务器上进行更改时，更改会传播到所有客户端，并调用回调函数。只传输增量数据。

## 用法<a href="#usage" id="usage"></a>

在您的 NetworkBehaviour 类中添加一个类型为 `SyncDictionary` 的字段。

{% hint style="info" %}
`SyncDictionary必须`声明为**只读**并在构造函数中初始化。
{% endhint %}

{% hint style="warning" %}
请注意，当您订阅回调时，字典已经初始化，因此您不会收到关于初始数据的调用，只会收到更新。
{% endhint %}

## 简单示例<a href="#simple-example" id="simple-example"></a>

```csharp
using UnityEngine;
using Mirror;

public struct Item
{
    public string name;
    public int hitPoints;
    public int durability;
}

public class ExamplePlayer : NetworkBehaviour
{
    public readonly SyncDictionary<string, Item> Equipment = new SyncDictionary<string, Item>();

    public override void OnStartServer()
    {
        Equipment.Add("head", new Item { name = "Helmet", hitPoints = 10, durability = 20 });
        Equipment.Add("body", new Item { name = "Epic Armor", hitPoints = 50, durability = 50 });
        Equipment.Add("feet", new Item { name = "Sneakers", hitPoints = 3, durability = 40 });
        Equipment.Add("hands", new Item { name = "Sword", hitPoints = 30, durability = 15 });
    }

    public override void OnStartClient()
    {
        // Equipment is already populated with anything the server set up
        // but we can subscribe to the callback in case it is updated later on
        Equipment.Callback += OnEquipmentChange;

        // Process initial SyncDictionary payload
        foreach (KeyValuePair<string, Item> kvp in Equipment)
            OnEquipmentChange(SyncDictionary<string, Item>.Operation.OP_ADD, kvp.Key, kvp.Value);
    }

    void OnEquipmentChange(SyncDictionary<string, Item>.Operation op, string key, Item item)
    {
        switch (op)
        {
            case SyncIDictionary<string, Item>.Operation.OP_ADD:
                // entry added
                break;
            case SyncIDictionary<string, Item>.Operation.OP_SET:
                // entry changed
                break;
            case SyncIDictionary<string, Item>.Operation.OP_REMOVE:
                // entry removed
                break;
            case SyncIDictionary<string, Item>.Operation.OP_CLEAR:
                // Dictionary was cleared
                break;
        }
    }
}
```

默认情况下，SyncDictionary使用[Dictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=netcore-3.1)来存储数据。如果您想使用不同的 `IDictionary` 实现，比如[SortedList](https://docs.microsoft.com/en-us/dotnet/api/system.collections.sortedlist?view=netcore-3.1)或[SortedDictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.sorteddictionary-2?view=netcore-3.1)，那么请使用 `SyncIDictionary` 并传递您想要使用的字典实例。例如：

```csharp
public class ExamplePlayer : NetworkBehaviour
{
    public readonly SyncIDictionary Equipment = 
        new SyncIDictionary(new SortedList());
}
```
