# 远程操作(Remote Actions)

网络系统有方法可以在网络上执行操作。这种类型的操作有时被称为远程过程调用(Remote Procedure Calls)。网络系统中有两种类型的RPCs，Commands - 从客户端调用并在服务器上运行；以及ClientRpc调用 - 在服务器上调用并在客户端上运行。

下面的图表显示了远程操作的方向：

![](<../../../.gitbook/assets/image (96).png>)

## Commands <a href="#commands" id="commands"></a>

Commands是从客户端的玩家对象发送到服务器上的玩家对象。出于安全考虑，默认情况下，Commands只能从您的玩家对象发送，因此您无法控制其他玩家的对象。您可以通过`[Command(requiresAuthority = false)]`来绕过权限检查。

要将函数转换为命令，将\[Command]自定义属性添加到函数中，并可选择为命名约定添加“Cmd”前缀。当在客户端调用时，此函数现在将在服务器上运行。任何[允许的数据类型](../data-types.md)的参数将自动传递到服务器与命令一起。

Commands函数应该具有前缀“Cmd”并且不能是静态的。这是在阅读调用命令的代码时的提示 - 此函数是特殊的，不像普通函数那样在本地调用。

```csharp
public class Player : NetworkBehaviour
{
    // assigned in inspector
    public GameObject cubePrefab;

    void Update()
    {
        if (!isLocalPlayer) return;

        if (Input.GetKey(KeyCode.X))
            CmdDropCube();
    }

    [Command]
    void CmdDropCube()
    {
        if (cubePrefab != null)
        {
            Vector3 spawnPos = transform.position + transform.forward * 2;
            Quaternion spawnRot = transform.rotation;
            GameObject cube = Instantiate(cubePrefab, spawnPos, spawnRot);
            NetworkServer.Spawn(cube);
        }
    }
}
```

小心每帧从客户端发送命令！这可能会导致大量的网络流量。

### 绕过权限检查

如果以下任一条件为真，则可以在非玩家对象上调用命令：

* 该对象是使用客户端权限生成的
* 该对象已使用`NetworkIdentity.AssignClientAuthority`设置了客户端权限
* 该Command的`requiresAuthority`选项设置为false。
  * 您可以在Command方法签名中包含一个可选的`NetworkConnectionToClient sender = null`参数，Mirror将为您填充发送客户端。
  * 不要尝试为此可选参数设置值...它将被忽略。

从这些对象发送的Commands在对象的服务器实例上运行，而不是在客户端的关联玩家对象上运行。

```csharp
public enum DoorState : byte
{
    Open, Closed, Locked
}

public class Door : NetworkBehaviour
{
    [SyncVar]
    public DoorState doorState;
    
    [Client]
    void OnMouseUpAsButton()
    {
        CmdSetDoorState();
    }

    [Command(requiresAuthority = false)]
    public void CmdSetDoorState(NetworkConnectionToClient sender = null)
    {
        bool hasDoorKey = sender.identity.GetComponent<PlayerState>().hasDoorKey;
        
        if (doorState == DoorState.Open)
        {
            doorState = hasDoorKey ? DoorState.Locked : DoorState.Closed;
            return;
        }
        
        if (doorState == DoorState.Locked && hasDoorKey)
        {
            doorState = DoorState.Open;
            return;
        }
        
        if (doorState == DoorState.Closed)
            doorState = DoorState.Open;
    }
}
```

## ClientRpc 调用 <a href="#clientrpc-calls" id="clientrpc-calls"></a>

ClientRpc 调用是从服务器上的对象发送到客户端上的对象。它们可以从任何已生成的具有 NetworkIdentity 的服务器对象发送。由于服务器具有权限，因此服务器对象能够发送这些调用没有安全问题。要将函数转换为 ClientRpc 调用，请向其添加 \[ClientRpc\] 自定义属性，并可选择为命名约定添加“Rpc”前缀。当在服务器上调用此函数时，该函数现在将在客户端上运行。任何 [允许的数据类型](../data-types.md) 的参数将自动通过 ClientRpc 调用传递给客户端。

ClientRpc 函数应具有前缀“Rpc”并且不能是静态的。这是在阅读调用该方法的代码时的一个提示 - 此函数是特殊的，不像普通函数那样在本地调用。

```csharp
public class Player : NetworkBehaviour
{
    int health;

    public void TakeDamage(int amount)
    {
        if (!isServer) return;

        health -= amount;
        RpcDamage(amount);
    }

    [ClientRpc]
    public void RpcDamage(int amount)
    {
        Debug.Log("Took damage:" + amount);
    }
}
```

当作为主机运行游戏并具有本地客户端时，即使本地客户端与服务器在同一进程中，ClientRpc 调用也将在本地客户端上调用。因此，对于 ClientRpc 谦虚，本地客户端和远程客户端的行为是相同的。

### 排除所有者

ClientRpc 消息仅发送给对象的观察者，根据其 [网络可见性](../../interest-management/)。玩家对象始终是自己的观察者。在某些情况下，您可能希望在调用 ClientRpc 时排除所有者客户端。这可以通过 `includeOwner` 选项来实现：`[ClientRpc(includeOwner = false)]`。

## TargetRpc 调用 <a href="#targetrpc-calls" id="targetrpc-calls"></a>

TargetRpc 函数由服务器上的用户代码调用，然后在指定的 `NetworkConnection` 的客户端对象上调用。RPC 调用的参数在网络上进行序列化，以便客户端函数以与服务器上函数相同的值调用。这些函数应以“Target”前缀开始，以符合命名约定，并且不能是静态的。

**上下文很重要：** (Context Matters)

* 如果你的 TargetRpc 方法的第一个参数是 `NetworkConnection`，那么无论上下文如何，该连接都将接收到消息。
* 如果第一个参数是其他任何类型，则包含你的 TargetRpc 的脚本对象的所有者客户端将接收到消息。

这个示例展示了客户端如何使用 Command 向服务器发出请求(`CmdMagic`)，通过在从该 Command 直接调用的 TargetRpc 中包含另一个玩家的 `connectionToClient` 作为参数：

```csharp
public class Player : NetworkBehaviour
{
    public int health;

    [Command]
    void CmdMagic(GameObject target, int damage)
    {
        target.GetComponent<Player>().health -= damage;

        NetworkIdentity opponentIdentity = target.GetComponent<NetworkIdentity>();
        TargetDoMagic(opponentIdentity.connectionToClient, damage);
    }

    [TargetRpc]
    public void TargetDoMagic(NetworkConnectionToClient target, int damage)
    {
        // This will appear on the opponent's client, not the attacking player's
        Debug.Log($"Magic Damage = {damage}");
    }

    // Heal thyself
    [Command]
    public void CmdHealMe()
    {
        health += 10;
        TargetHealed(10);
    }

    [TargetRpc]
    public void TargetHealed(int amount)
    {
        // No NetworkConnection parameter, so it goes to owner
        Debug.Log($"Health increased by {amount}");
    }
}
```

## 远程操作的参数 <a href="#arguments-to-remote-actions" id="arguments-to-remote-actions"></a> (Arguments to Remote Actions)

传递给命令和 ClientRpc 调用的参数将被序列化并通过网络发送。你可以使用任何[支持的 Mirror 类型](../data-types.md)。

远程操作的参数不能是游戏对象的子组件，比如脚本实例或 Transforms。
