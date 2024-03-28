# SyncHashSet(同步哈希集合)

`SyncHashSet`是类似于 C# [HashSet](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.hashset-1)(哈希集合)的集合，它们会将其内容从服务器同步到客户端。

一个 SyncHashSet 可以包含任何[支持的 Mirror 类型](../data-types.md)(数据类型)

## 用法 <a href="#usage" id="usage"></a>

{% hint style="info" %}
SyncHashSet 必须声明为 **readonly** 并在构造函数中初始化。
{% endhint %}

在你的 NetworkBehaviour 类中添加一个 SyncHashSet 字段。例如：

```csharp
public class Player : NetworkBehaviour
{
    [SerializeField]
    public readonly SyncHashSet<string> skills = new SyncHashSet<string>();

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

你也可以检测 SyncHashSet 何时发生变化。这对于在客户端刷新角色或确定何时需要更新数据库非常有用。

通常在 `Start`、`OnClientStart` 或 `OnServerStart` 中订阅 Callback 事件。

{% hint style="warning" %}
请注意，当你订阅时，集合已经被填充，因此你不会收到初始数据的调用，只有更新。
{% endhint %}

```csharp
public class Player : NetworkBehaviour
{
    [SerializeField]
    public readonly SyncHashSet<string> buffs = new SyncHashSet<string>();

    // this will add the delegate on the client.
    // Use OnStartServer instead if you want it on the server
    public override void OnStartClient()
    {
        buffs.Callback += OnBuffsChanged;

        // Process initial SyncHashSet payload
        foreach (string buff in buffs)
            OnBuffsChanged(SyncSet<string>.Operation.OP_ADD, buff);
    }

    // SyncHashSet inherits from SyncSet so use SyncSet here
    void OnBuffsChanged(SyncSet<string>.Operation op, string buff)
    {
        switch (op)
        {
            case SyncSet<string>.Operation.OP_ADD:
                // Added a buff to the character
                break;
            case SyncSet<string>.Operation.OP_REMOVE:
                // Removed a buff from the character
                break;
            case SyncSet<string>.Operation.OP_CLEAR:
                // Cleared all buffs from the character
                break;
        }
    }
}
```
