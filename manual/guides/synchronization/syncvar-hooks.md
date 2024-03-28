# SyncVar Hooks (同步变量钩子)

`Hook`属性可用于指定在客户端上`SyncVar`更改值时要调用的函数。

* `Hook`方法必须有两个与`SyncVar`属性相同类型的参数。一个用于旧值，一个用于新值。
* `Hook`总是在设置属性值后调用。您不需要自己设置它。
* `Hook`仅对更改的值触发，并且在检视器中更改值不会触发更新。
* 从版本11.1.4（2020年3月）开始，钩子可以是虚方法，并且可以在派生类中重写。

以下是一个简单的示例，当玩家在服务器上生成时，为每个玩家分配随机颜色。所有客户端将以正确的颜色看到所有玩家，即使他们稍后加入也是如此。

> 注意：`Hook`方法的签名在版本9.0（2020年2月）中更改为具有2个参数（旧值和新值）。如果您使用的是旧版本，则`Hook`方法只有一个参数（新值）。

```csharp
using UnityEngine;
using Mirror;

public class PlayerController : NetworkBehaviour
{
    [SyncVar(hook = nameof(SetColor))]
    Color playerColor = Color.black;

    // Unity makes a clone of the Material every time GetComponent().material is used.
    // Cache it here and Destroy it in OnDestroy to prevent a memory leak.
    Material cachedMaterial;

    public override void OnStartServer()
    {
        base.OnStartServer();
        playerColor = Random.ColorHSV(0f, 1f, 1f, 1f, 0.5f, 1f);
    }

    void SetColor(Color oldColor, Color newColor)
    {
        if (cachedMaterial == null)
            cachedMaterial = GetComponent().material;

        cachedMaterial.color = newColor;
    }

    void OnDestroy()
    {
        Destroy(cachedMaterial);
    }
}
```

## Hook 调用顺序 <a href="#hook-call-order" id="hook-call-order"></a>

`Hook`按照文件中定义的`SyncVar`的顺序调用。

```csharp
public class MyBehaviour : NetworkBehaviour 
{
    [SyncVar] 
    int X;

    [SyncVar(hook = nameof(Hook1))] 
    int Y;

    [SyncVar(hook = nameof(Hook2))]
    int Z;
}
```

如果在服务器上同时设置X、Y和Z，则调用顺序将是：

1. 设置X值
2. 设置Y值
3. 调用`Hook1`
4. 设置Z值
5. 调用`Hook2`
