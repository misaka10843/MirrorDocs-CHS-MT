# SyncEvent (Obsolete) (同步事件)

{% hint style="warning" %}
在版本18.0.0中已移除SyncEvents，请查看此[Issue](https://github.com/vis2k/Mirror/pull/2178)获取更多信息。
{% endhint %}

这是一个可以放在NetworkBehaviour类中的事件上的属性，允许在服务器调用事件时在客户端上调用它们。

SyncEvent事件由服务器上的用户代码调用，然后在连接到服务器的客户端上调用相应的客户端对象。事件调用的参数在网络上进行序列化，因此客户端事件将以与服务器上方法相同的值调用。这些事件必须以前缀"Event"开头。

```csharp
using UnityEngine;
using Mirror;

public class DamageClass : NetworkBehaviour
{
    public delegate void TakeDamageDelegate(int amount, float dir);

    [SyncEvent]
    public event TakeDamageDelegate EventTakeDamage;

    [Command]
    public void CmdDoMe(int val)
    {
        EventTakeDamage(val, 1.0f);
    }
}

public class Other : NetworkBehaviour
{
    public DamageClass damager;
    int health = 100;

    void Start()
    {
        if (NetworkClient.active)
            damager.EventTakeDamage += TakeDamage;
    }

    public void TakeDamage(int amount, float dir)
    {
        health -= amount;
    }
}
```

SyncEvents允许将网络动作传播到附加到对象的其他脚本。在上面的示例中，Other类在DamageClass上注册TakeDamage事件。当服务器上的DamageClass发生事件时，Other类上的TakeDamage()方法将在客户端对象上被调用。这允许创建可扩展的模块化网络感知系统，可以通过响应由它们生成的事件来扩展新脚本。
