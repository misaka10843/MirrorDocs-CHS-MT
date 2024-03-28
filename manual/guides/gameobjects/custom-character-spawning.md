# 自定义角色生成 (Custom Character Spawning)

许多游戏需要角色定制。您可能希望选择头发、眼睛、皮肤、身高、种族等的颜色。

默认情况下，Mirror会为您实例化玩家。虽然这很方便，但可能会阻止您进行自定义。Mirror提供了覆盖玩家创建并自定义的选项。

1. 如果尚未这样做，请创建一个扩展`NetworkManager`的类。例如:

```csharp
public class MMONetworkManager : NetworkManager
{
    ...
}
```

并将其用作您的网络管理器。

1. 在检视器中打开您的网络管理器并禁用“自动创建玩家”布尔值。
2. 创建描述您的玩家的消息。例如:

```csharp
public struct CreateMMOCharacterMessage : NetworkMessage
{
    public Race race;
    public string name;
    public Color hairColor;
    public Color eyeColor;
}

public enum Race
{
    None,
    Elvish,
    Dwarvish,
    Human
}
```

1. 创建您的玩家预制体(根据需要创建多个)并将它们添加到您的网络管理器中的“注册可生成预制体”中，或者在检视器中的玩家预制体字段中添加单个预制体。
2. 发送您的消息并注册一个玩家:

```csharp
public class MMONetworkManager : NetworkManager
{
    public override void OnStartServer()
    {
        base.OnStartServer();

        NetworkServer.RegisterHandler<CreateMMOCharacterMessage>(OnCreateCharacter);
    }

    public override void OnClientConnect()
    {
        base.OnClientConnect();

        // you can send the message here, or wherever else you want
        CreateMMOCharacterMessage characterMessage = new CreateMMOCharacterMessage
        {
            race = Race.Elvish,
            name = "Joe Gaba Gaba",
            hairColor = Color.red,
            eyeColor = Color.green
        };

        NetworkClient.Send(characterMessage);
    }

    void OnCreateCharacter(NetworkConnectionToClient conn, CreateMMOCharacterMessage message)
    {
        // playerPrefab is the one assigned in the inspector in Network
        // Manager but you can use different prefabs per race for example
        GameObject gameobject = Instantiate(playerPrefab);

        // Apply data from the message however appropriate for your game
        // Typically Player would be a component you write with syncvars or properties
        Player player = gameobject.GetComponent();
        player.hairColor = message.hairColor;
        player.eyeColor = message.eyeColor;
        player.name = message.name;
        player.race = message.race;

        // call this to use this gameobject as the primary controller
        NetworkServer.AddPlayerForConnection(conn, gameobject);
    }
}
```

## 就绪状态 (Ready State) <a href="#ready-state" id="ready-state"></a>

除了玩家之外，客户端连接还有一个“就绪”状态。主机向准备好的客户端发送有关生成的游戏对象和状态同步更新的信息；未准备好的客户端不会收到这些更新。当客户端最初连接到服务器时，它是不准备好的。在这种非准备好状态下，客户端可以执行不需要与服务器上的游戏状态进行实时交互的操作，例如加载场景、允许玩家选择角色或填写登录框。一旦客户端完成了所有的游戏前工作，并且所有的资源都已加载，它可以调用`NetworkClient.Ready`进入“就绪”状态。上面简单的示例演示了就绪状态的实现；因为使用`NetworkServer.AddPlayerForConnection`添加玩家也会将客户端放入就绪状态（如果尚未处于该状态）。

客户端可以在没有准备好的情况下发送和接收网络消息，这也意味着它们可以在没有活动玩家游戏对象的情况下这样做。因此，处于菜单或选择屏幕的客户端可以连接到游戏并与之交互，即使它们没有玩家游戏对象。有关发送消息而不使用命令和RPC调用的更多详细信息，请参阅[网络消息](../communications/network-messages.md)文档。

## 切换玩家（Switching Players）

要替换连接的玩家游戏对象，请使用 `NetworkServer.ReplacePlayerForConnection`。这在限制玩家在某些时候可以发出的命令时非常有用，比如在预备游戏房间屏幕中。这个函数接受与 `AddPlayerForConnection` 相同的参数，但允许为该连接已经存在一个玩家。旧的玩家游戏对象不必被销毁。`NetworkRoomManager` 使用这种技术，当房间中的所有玩家准备就绪时，从 `NetworkRoomPlayer` 游戏对象切换到游戏玩家游戏对象。

您还可以使用 `ReplacePlayerForConnection` 来重新生成玩家或更改代表玩家的对象。在某些情况下，最好只是禁用游戏对象，并在重新生成时重置其游戏属性。以下代码示例演示了如何实际替换玩家游戏对象为一个新的游戏对象：

```csharp
public class MyNetworkManager : NetworkManager
{
    public void ReplacePlayer(NetworkConnectionToClient conn, GameObject newPrefab)
    {
        // Cache a reference to the current player object
        GameObject oldPlayer = conn.identity.gameObject;

        // Instantiate the new player object and broadcast to clients
        // Include true for keepAuthority paramater to prevent ownership change
        NetworkServer.ReplacePlayerForConnection(conn, Instantiate(newPrefab), true);

        // Remove the previous player object that's now been replaced
        // Delay is required to allow replacement to complete.
        Destroy(oldPlayer, 0.1f);
    }
}
```

如果连接的玩家游戏对象被销毁，则该客户端无法执行命令。但是，他们仍然可以发送网络消息。

要使用 `ReplacePlayerForConnection`，您必须拥有玩家客户端的 `NetworkConnection` 游戏对象，以建立游戏对象和客户端之间的关系。这通常是 `NetworkBehaviour` 类上的 `connectionToClient` 属性，但如果旧的玩家已经被销毁，则可能无法立即获得。

要找到连接，有一些可用的列表。如果使用 `NetworkRoomManager`，则房间玩家可在 `roomSlots` 中找到。`NetworkServer` 也有 `connections` 的列表。
