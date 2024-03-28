# SyncLists(同步列表)

SyncLists是基于数组的列表，类似于C# [List](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1?view=netframework-4.7.2)，它们会将内容从服务器同步到客户端。

一个SyncList可以包含任何[支持的Mirror类型](../data-types.md)。

## 与UNET的不同 <a href="#differences-with-hlapi" id="differences-with-hlapi"></a>

UNET也支持SyncLists，但我们对它们进行了重新设计，使其更高效且更易于使用。一些关键的区别包括：

* 在UNET中，当SyncLists更改时会立即同步。如果您添加了10个元素，那意味着10个单独的消息。Mirror使用SyncVars来同步SyncLists。这10个元素和其他SyncVars被批量打包到单个消息中。Mirror在同步列表时还会尊重同步间隔。
* 在UNET中，如果您想要一个结构体列表，您必须使用`SyncListStruct`，我们将其更改为`SyncList`
* 在UNET中，回调是一个委托。在Mirror中，我们将其更改为事件，这样您可以添加多个订阅者。
* 在UNET中，回调告诉您操作和索引。在Mirror中，回调还接收一个项目。我们进行了这个更改，以便我们可以知道哪个项目被移除。
* 在UNET中，您必须创建一个从SyncList继承的类。在Mirror中，您可以直接使用SyncList（从版本20.0.0开始）

## 用法 <a href="#usage" id="usage"></a>

在您的NetworkBehaviour类中添加一个SyncList字段。

{% hint style="info" %}
SyncList必须声明为**只读**并在构造函数中初始化。
{% endhint %}

```csharp
public struct Item
{
    public string name;
    public int amount;
    public Color32 color;
}

public class Player : NetworkBehaviour
{
    public readonly SyncList<Item> inventory = new SyncList<Item>();

    public int coins = 100;

    [Command]
    public void CmdPurchase(string itemName)
    {
        if (coins > 10)
        {
            coins -= 10;
            Item item = new Item
            {
                name = "Sword",
                amount = 3,
                color = new Color32(125, 125, 125, 255)
            };

            // during next synchronization,  all clients will see the item
            inventory.Add(item);
        }
    }
}
```

您还可以在客户端或服务器端检测SyncList何时更改。这对于在添加装备时刷新您的角色或确定何时需要更新数据库非常有用。通常在`Start`、`OnClientStart`或`OnServerStart`期间订阅Callback事件。

{% hint style="warning" %}
请注意，当您订阅时，列表已经被填充，因此您不会收到初始数据的调用，只会收到更新。
{% endhint %}

```csharp
class Player : NetworkBehaviour {

    public override void OnStartClient()
    {
        inventory.Callback += OnInventoryUpdated;
        
        // Process initial SyncList payload
        for (int index = 0; index < inventory.Count; index++)
            OnInventoryUpdated(SyncList<Item>.Operation.OP_ADD, index, new Item(), inventory[index]);
    }

    void OnInventoryUpdated(SyncList<Item>.Operation op, int index, Item oldItem, Item newItem)
    {
        switch (op)
        {
            case SyncList<Item>.Operation.OP_ADD:
                // index is where it was added into the list
                // newItem is the new item
                break;
            case SyncList<Item>.Operation.OP_INSERT:
                // index is where it was inserted into the list
                // newItem is the new item
                break;
            case SyncList<Item>.Operation.OP_REMOVEAT:
                // index is where it was removed from the list
                // oldItem is the item that was removed
                break;
            case SyncList<Item>.Operation.OP_SET:
                // index is of the item that was changed
                // oldItem is the previous value for the item at the index
                // newItem is the new value for the item at the index
                break;
            case SyncList<Item>.Operation.OP_CLEAR:
                // list got cleared
                break;
        }
    }
}
```
