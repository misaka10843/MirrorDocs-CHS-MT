# SyncSortedSet(同步排序集合)

`SyncSortedSet`是类似于C# [SortedSet](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.sortedset-1)(排序集合)的集合，它们会将其内容从服务器同步到客户端。

与SyncHashSets不同，SyncSortedSet中的所有元素在插入时都会被排序。请注意这会对性能产生一些影响。

SyncSortedSet可以包含任何[支持的 Mirror 类型](../data-types.md)(数据类型)

## 用法 <a href="#usage" id="usage"></a>

{% hint style="info" %}
SyncSortedSet必须声明为**readonly**并在构造函数中初始化。
{% endhint %}

在您的NetworkBehaviour类中添加一个SyncSortedSet字段。例如：

```csharp
class Player : NetworkBehaviour
{
    public readonly SyncSortedSet<string> skills = new SyncSortedSet<string>();
    int skillPoints = 10;

    [Command]
    public void CmdLearnSkill(string skillName)
    {
        if (skillPoints > 1)
        {
            skillPoints--;
            skills.Add(skillName);
        }
    }
}
```

您还可以检测SyncSortedSet何时发生变化。这对于在客户端刷新角色或确定何时需要更新数据库非常有用。通常在`Start`、`OnClientStart`或`OnServerStart`期间订阅Callback事件。

{% hint style="warning" %}
请注意，当您订阅时，集合已经被初始化，因此您不会收到关于初始数据的调用，只会收到更新。
{% endhint %}

```csharp
class Player : NetworkBehaviour
{
    [SerializeField]
    public readonly SyncSortedSet<string> buffs = new SyncSortedSet<string>();

    // this will add the delegate on the client.
    // Use OnStartServer instead if you want it on the server
    public override void OnStartClient()
    {
        buffs.Callback += OnBuffsChanged;

        // Process initial SyncSortedSet payload
        foreach (string buff in buffs)
            OnBuffsChanged(SyncSortedSet<string>.Operation.OP_ADD, buff);
    }

    // SyncSortedSet inherits from SyncSet so use SyncSet here
    void OnBuffsChanged(SyncSortedSet<string>.Operation op, string buff)
    {
        switch (op)
        {
            case SyncSortedSet<string>.Operation.OP_ADD:
                // Added a buff to the character
                break;
            case SyncSortedSet<string>.Operation.OP_REMOVE:
                // Removed a buff from the character
                break;
            case SyncSortedSet<string>.Operation.OP_CLEAR:
                // cleared all buffs from the character
                break;
        }
    }
}
```
