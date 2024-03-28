# 数据类型(Data types)

客户端和服务器可以通过[远程操作](communications/remote-actions.md)(Remote Actions)、[状态同步](synchronization/)(State Synchronization)或通过[网络消息](communications/network-messages.md)(Network Messages)相互传递数据。

Mirror支持多种数据类型，您可以在这些情况下使用，包括：

- 基本的C#类型(byte, short, int, long, uint, ushort, ulong, float, double, char, string等)
- 内置的Unity数学类型(Vector3, Quaternion, Rect, Plane等)
- 内置的Unity类型，它们在底层是结构体(Color, Sprite, Texture2D, Ray等)
- URI
- `NetworkIdentity`(网络标识), `NetworkBehaviour`(网络行为)
  - **这些不应该在SyncVars或Sync\*集合或Rpc中使用，因为如果对应的对象尚未生成，它们在客户端上将为null。**
- 具有已经网络生成的`NetworkIdentity`组件的游戏对象
  - **不是**预制体!
  - 请查看下面[游戏对象](gameobjects/)(GameObjects)部分中的重要细节。
- 具有上述任何类型的结构体
  - 您必须替换整个结构体值，而不仅仅是更改其属性
  - 建议实现IEquatable以避免装箱(boxing)，并且将结构体设置为只读，因为修改其中一个属性**不会**导致重新同步
- 类，只要每个字段都具有支持的数据类型
  - 您必须替换整个类值，而不仅仅是更改其属性
  - **这些将分配垃圾(garbage)**，并且每次发送时都会在接收端实例化新的对象。
- ScriptableObject，只要每个字段都具有支持的数据类型
  - **这些将分配垃圾(garbage)**，并且每次发送时都会在接收端实例化新的对象。
  - 请查看下面[ScriptableObjects](data-types.md#scriptable-objects)部分中的详细信息。
- 上述任何类型的数组
  - 与[Sync\*集合](synchronization/)(Sync\* collections)不兼容
- 上述任何类型的ArraySegments
  - 与[Sync\*集合](synchronization/)(Sync\* collections)不兼容

## 游戏对象(Game Objects) <a href="#game-objects" id="game-objects"></a>

在SyncVars、SyncLists和SyncDictionaries中的游戏对象在某些情况下是脆弱的，应谨慎使用。

* 只要游戏对象在服务器和客户端上都_已存在_，引用就应该正常。

当同步数据到达客户端时，引用的游戏对象可能在该客户端上尚不存在，导致同步数据中出现空值。这是因为在内部，Mirror 会传递 `NetworkIdentity` 的 `netId`，并尝试在客户端的 `NetworkClient.spawned` 字典中查找它。

如果对象尚未在客户端上生成，就不会找到匹配项。它可能在同一负载中，尤其是对于加入的客户端，但在另一个对象的同步数据之后。\
&#x20;它也可能为空，因为游戏对象由于网络可见性而被从客户端排除，例如[`兴趣管理`](../interest-management/)。

您可能会发现，将 `NetworkIdentity.netID` (uint) 进行同步，然后在 `NetworkClient.spawned` 中自行查找对象更加健壮，也许可以在协程中进行：

```csharp
    public GameObject target;

    [SyncVar(hook = nameof(OnTargetChanged))]
    public uint targetID;

    void OnTargetChanged(uint _, uint newValue)
    {
        target = null;
        
        if (NetworkClient.spawned.TryGetValue(targetID, out NetworkIdentity identity))
            target = identity.gameObject;
        else
            StartCoroutine(SetTarget());
    }

    IEnumerator SetTarget()
    {
        while (target == null)
        {
            yield return null;
            if (NetworkClient.spawned.TryGetValue(targetID, out NetworkIdentity identity))
                target = identity.gameObject;
        }
    }
```

## 自定义数据类型 <a href="#custom-data-types" id="custom-data-types"></a>

有时您可能不希望 Mirror 为您自定义的类型生成序列化。例如，您可能希望仅序列化任务数据的任务 id，接收方可以通过预定义列表或数据库中的 id 查找任务详细信息。

有时您可能希望序列化使用 Mirror 不支持的不同类型的数据，例如 DateTime。

您可以通过向 `NetworkWriter` 和 `NetworkReader` 添加扩展方法来为任何类型添加支持。例如，要为 `DateTime` 添加支持，请在项目的某个位置添加以下内容：

```csharp
public static class DateTimeReaderWriter
{
      public static void WriteDateTime(this NetworkWriter writer, DateTime dateTime)
      {
          writer.WriteInt64(dateTime.Ticks);
      }
     
      public static DateTime ReadDateTime(this NetworkReader reader)
      {
          return new DateTime(reader.ReadInt64());
      }
}
```

...然后您可以在您的 `[Command]` 或 `SyncList` 中使用 `DateTime`

## 继承和多态 <a href="#inheritance-and-polymorphism" id="inheritance-and-polymorphism"></a>

有时您可能希望将多态数据类型发送到您的命令中。Mirror 不会序列化类型名称以保持消息的小巧和出于安全原因，因此 Mirror 无法通过查看消息来确定接收到的对象的类型。

> **This code does not work out of the box.** (此代码不能直接使用)

```csharp
class Item 
{
    public string name;
}

class Weapon : Item
{
    public int hitPoints;
}

class Armor : Item
{
    public int hitPoints;
    public int level;
}

class Player : NetworkBehaviour
{
    [Command]
    void CmdEquip(Item item)
    {
        // IMPORTANT: this does not work. Mirror will pass you an object of type item
        // even if you pass a weapon or an armor.
        if (item is Weapon weapon)
        {
            // The item is a weapon, 
            // maybe you need to equip it in the hand
        }
        else if (item is Armor armor)
        {
            // you might want to equip armor in the body
        }
    }

    [Command]
    void CmdEquipArmor(Armor armor)
    {
        // IMPORTANT: this does not work either, you will receive an armor, but 
        // the armor will not have a valid Item.name, even if you passed an armor with name
    }
}
```

如果为`Item`类型提供自定义序列化器，CmdEquip将起作用。例如：

public static class ItemSerializer 
{
    const byte WEAPON = 1;
    const byte ARMOR = 2;

    public static void WriteItem(this NetworkWriter writer, Item item)
    {
        if (item is Weapon weapon)
        {
            writer.WriteByte(WEAPON);
            writer.WriteString(weapon.name);
            writer.WritePackedInt32(weapon.hitPoints);
        }
        else if (item is Armor armor)
        {
            writer.WriteByte(ARMOR);
            writer.WriteString(armor.name);
            writer.WritePackedInt32(armor.hitPoints);
            writer.WritePackedInt32(armor.level);
        }
    }

    public static Item ReadItem(this NetworkReader reader)
    {
        byte type = reader.ReadByte();
        switch(type)
        {
            case WEAPON:
                return new Weapon
                {
                    name = reader.ReadString(),
                    hitPoints = reader.ReadPackedInt32()
                };
            case ARMOR:
                return new Armor
                {
                    name = reader.ReadString(),
                    hitPoints = reader.ReadPackedInt32(),
                    level = reader.ReadPackedInt32()
                };
            default:
                throw new Exception($"Invalid weapon type {type}");
        }
    }
}
```

## ScriptableObjects <a href="#scriptable-objects" id="scriptable-objects"></a>

People often want to send scriptable objects from the client or server. For example, you may have a bunch of swords created as scriptable objects and you want put the equipped sword in a syncvar. This will work fine, Mirror will generate a reader and writer for scriptable objects by calling ScriptableObject.CreateInstance and copy all the data.

However the generated reader and writer are not suitable for every occasion. Scriptable objects often reference other assets such as textures, prefabs, or other types that can't be serialized. Scriptable objects are often saved in the in the Resources folder. Scriptable objects sometimes have a large amount of data in them. The generated reader and writers may not work or may be inneficient for these situations.

Instead of passing the scriptable object data, you can pass the name and the other side can lookup the same object by name. This way you can have any kind of data in your scriptable object. You can do that by providing a custom reader and writer. Here is an example:

```csharp
[CreateAssetMenu(fileName = "New Armor", menuName = "Armor Data")]
class Armor (护甲) : ScriptableObject
{
    public int Hitpoints; // 生命值
    public int Weight; // 重量
    public string Description; // 描述
    public Texture2D Icon; // 图标
    // ...
}

public static class ArmorSerializer (护甲序列化器)
{
    public static void WriteArmor(this NetworkWriter writer, Armor armor)
    {
       // 无需序列化数据，只需护甲的名称
       writer.WriteString(armor.name);
    }

    public static Armor ReadArmor(this NetworkReader reader)
    {
        // 通过名称加载相同的护甲。数据将来自Resources文件夹中的资源
        return Resources.Load(reader.ReadString());
    }
}
```
