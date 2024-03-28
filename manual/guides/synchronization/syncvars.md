# SyncVars(同步变量)

SyncVars是从NetworkBehaviour类继承的属性，这些属性从服务器同步到客户端。当游戏对象被生成或新玩家加入进行中的游戏时，它们会接收到所有可见的网络对象上的所有SyncVars的最新状态。使用`SyncVar`自定义属性来指定您想要同步的脚本中的变量。

在调用`OnStartClient()`之前，SyncVars的状态会应用到客户端的游戏对象上，因此在`OnStartClient()`内对象的状态始终是最新的。

SyncVars可以使用[Mirror支持的任何类型](../data-types.md)。您可以在单个NetworkBehaviour脚本上有多达64个SyncVars，包括SyncLists（请参见下面的下一节）。

当SyncVar的值发生变化时，服务器会自动发送SyncVar更新，因此您无需跟踪它们何时更改或自己发送有关更改的信息。在检视器中更改值不会触发更新。

> [SyncVar hook](syncvar-hooks.md)属性可用于指定在客户端SyncVar更改值时要调用的方法。

## SyncVar示例 <a href="#syncvar-example" id="syncvar-example"></a>

假设我们有一个名为Enemy的带有脚本的网络对象：

```csharp
public class Enemy : NetworkBehaviour
{
    [SyncVar]
    public int health = 100;

    void OnMouseUp()
    {
        NetworkIdentity ni = NetworkClient.connection.identity;
        PlayerController pc = ni.GetComponent<PlayerController>();
        pc.currentTarget = gameObject;
    }
}
```

`PlayerController`可能如下所示：

```csharp
public class PlayerController : NetworkBehaviour
{
    public GameObject currentTarget;

    void Update()
    {
        if (isLocalPlayer)
            if (currentTarget != null &&currentTarget.tag == "Enemy")
                if (Input.GetKeyDown(KeyCode.X))
                    CmdShoot(currentTarget);
    }

    [Command]
    public void CmdShoot(GameObject enemy)
    {
        // Do your own shot validation here because this runs on the server
        enemy.GetComponent<Enemy>().health -= 5;
    }
}
```

在这个示例中，当玩家点击Enemy时，网络敌人游戏对象被分配给`PlayerController.currentTarget`。当玩家按下X键时，选择了正确目标，该目标通过一个在服务器上运行的Command传递，以减少`health` SyncVar。所有客户端都将更新为该新值。然后您可以在敌人上有一个UI来显示当前值。

## 类继承 <a href="#class-inheritance" id="class-inheritance"></a>

SyncVars与类继承一起工作。考虑以下示例：

```csharp
class Pet : NetworkBehaviour
{
    [SyncVar] 
    string name;
}

class Cat : Pet
{
    [SyncVar]
    public Color32 color;
}
```

您可以将Cat组件附加到您的猫预制件上，它将同步其`name`和`color`。

> **警告** `Cat`和`Pet`应该在同一个程序集中。如果它们在不同的程序集中，请确保不直接从`Cat`内部更改`name`，而是在`Pet`中添加一个方法。 (Warning: Both `Cat` and `Pet` should be in the same assembly. If they are in separate assemblies, make sure not to change `name` from inside `Cat` directly, add a method to `Pet` instead.)
