# 自定义生成函数（Custom Spawn Functions）

您可以使用生成处理函数来自定义在客户端创建生成的游戏对象时的默认行为。生成处理函数确保您完全控制如何生成游戏对象，以及如何销毁它。

使用 `NetworkClient.RegisterSpawnHandler` 或 `NetworkClient.RegisterPrefab` 来注册生成和销毁客户端游戏对象的函数。服务器直接创建游戏对象，然后通过此功能在客户端生成它们。这些函数接受资产 ID 或预制体以及两个函数委托：一个用于处理在客户端创建游戏对象，另一个用于处理在客户端销毁游戏对象。资产 ID 可以是动态的，也可以是您想要生成的预制体游戏对象上找到的资产 ID。

生成 / 销毁委托看起来类似于：

**生成处理函数**

```csharp
GameObject SpawnDelegate(Vector3 position, System.Guid assetId) 
{
    // do stuff here
}
```

或

```csharp
GameObject SpawnDelegate(SpawnMessage msg) 
{
    // do stuff here
}
```

**销毁处理函数**

```csharp
void UnSpawnDelegate(GameObject spawned) 
{
    // do stuff here
}
```

当预制体保存时，其 `assetId` 字段将自动设置。如果要在运行时创建预制体，您将需要生成一个新的 GUID。

### **在运行时生成预制体**

```csharp
// generate a new unique assetId 
System.Guid creatureAssetId = System.Guid.NewGuid();

// register handlers for the new assetId
NetworkClient.RegisterSpawnHandler(creatureAssetId, SpawnCreature, UnSpawnCreature);
```

**使用现有预制体**

```csharp
// 注册您想要自定义生成的预制体并传入处理程序
NetworkClient.RegisterPrefab(coinAssetId, SpawnCoin, UnSpawnCoin);
```

**在服务器上生成**

```csharp
// 生成一个硬币 - 在客户端调用 SpawnCoin
NetworkServer.Spawn(gameObject, coinAssetId);
```

生成函数本身是使用委托签名实现的。这是硬币生成器。`SpawnCreature` 看起来一样，但具有不同的生成逻辑：

```csharp
public GameObject SpawnCoin(SpawnMessage msg)
{
    return Instantiate(m_CoinPrefab, msg.position, msg.rotation);
}

public void UnSpawnCoin(GameObject spawned)
{
    Destroy(spawned);
}
```

在使用自定义生成函数时，有时可以无需销毁游戏对象而取消生成它们。这可以通过调用 `NetworkServer.UnSpawn` 来实现。这会导致对象在服务器上被 `Reset`，并向客户端发送 `ObjectDestroyMessage`。`ObjectDestroyMessage` 将导致在客户端调用自定义取消生成函数。如果没有取消生成函数，则对象将被 `Destroy`。

注意，在主机上，本地客户端不会为游戏对象生成，因为它们已经存在于服务器上。这也意味着不会调用生成或销毁处理函数。

### 游戏对象池

为了避免频繁实例化和销毁使用频繁的游戏对象，将它们放入对象池可能会更有用。

![](../../../.gitbook/assets/2022-04-04\_20-21-49@2x.png)

以下是一个设置简单游戏对象池系统以及自定义生成处理程序的示例。生成和销毁将游戏对象放入或移出对象池。

```csharp
using UnityEngine;

namespace Mirror.Examples
{
    public class PrefabPool : MonoBehaviour
    {
        // singleton for easier access from other scripts
        public static PrefabPool singleton;

        [Header("Settings")]
        public GameObject prefab;

        [Header("Debug")]
        public int currentCount;
        public Pool<GameObject> pool;

        void Start()
        {
            InitializePool();
            singleton = this;
            NetworkClient.RegisterPrefab(prefab, SpawnHandler, UnspawnHandler);
        }

        // used by NetworkClient.RegisterPrefab
        GameObject SpawnHandler(SpawnMessage msg) => Get(msg.position, msg.rotation);

        // used by NetworkClient.RegisterPrefab
        void UnspawnHandler(GameObject spawned) => Return(spawned);

        void OnDestroy()
        {
            NetworkClient.UnregisterPrefab(prefab);
        }

        void InitializePool()
        {
            // create pool with generator function
            pool = new Pool<GameObject>(CreateNew, 5);
        }

        GameObject CreateNew()
        {
            // use this object as parent so that objects dont crowd hierarchy
            GameObject next = Instantiate(prefab, transform);
            next.name = $"{prefab.name}_pooled_{currentCount}";
            next.SetActive(false);
            currentCount++;
            return next;
        }

        // Used to take Object from Pool.
        // Should be used on server to get the next Object
        // Used on client by NetworkClient to spawn objects
        public GameObject Get(Vector3 position, Quaternion rotation)
        {
            GameObject next = pool.Get();

            // set position/rotation and set active
            next.transform.position = position;
            next.transform.rotation = rotation;
            next.SetActive(true);
            return next;
        }

        // Used to put object back into pool so they can b
        // Should be used on server after unspawning an object
        // Used on client by NetworkClient to unspawn objects
        public void Return(GameObject spawned)
        {
            // disable object
            spawned.SetActive(false);

            // add back to pool
            pool.Return(spawned);
        }
    }
}

```

要使用此对象池，请将`PrefabPool`组件（上面的代码）添加到NetworkManager。然后，将要多次生成的预制体拖放到Prefab字段中。

{% hint style="warning" %}
确保从NetworkManager的可生成预制体列表中移除预制体。应该只有一种方法来生成它。否则，Mirror会显示警告。
{% endhint %}

我们可以修改我们的坦克示例来展示对象池系统。

打开Tank.cs并找到CmdFire函数：

```csharp
[Command]
void CmdFire()
{
    GameObject projectile = Instantiate(projectilePrefab, projectileMount.position, projectileMount.rotation);
    NetworkServer.Spawn(projectile);
    RpcOnFire();
}
```

不要实例化，而是从对象池中获取一个预制体：

```csharp
[Command]
void CmdFire()
{
    GameObject projectile = PrefabPool.singleton.Get(projectileMount.position, projectileMount.rotation);
    NetworkServer.Spawn(projectile);
    RpcOnFire();
}
```

Projectile.cs当前通过GameObject.Destroy销毁自身：

```csharp
[Server]
void DestroySelf()
{
    NetworkServer.Destroy(gameObject);
}
```

相反，我们只需将其取消生成并将其返回到对象池：

```csharp
[Server]
void DestroySelf()
{
    // return to prefab pool
    NetworkServer.UnSpawn(gameObject);
    PrefabPool.singleton.Return(gameObject);
}
```

按下播放并发射一些抛射物。注意到没有任何实例化。相反，NetworkManager有一组被禁用的子对象，直到需要它们为止。

![](../../../.gitbook/assets/2022-04-04\_20-22-58@2x.png)
