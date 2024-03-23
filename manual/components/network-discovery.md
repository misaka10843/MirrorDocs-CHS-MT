# 网络发现

假设你和朋友在一起。他在主机模式下开始了一场游戏，而你想要加入他。你的手机如何找到他的位置呢？找出他的IP地址并不直观，也不是孩子们可以做到的事情。

为了解决这个问题，你可以使用网络发现。当你的游戏启动时，它会在当前网络中发送一条消息询问：“是否有任何可用的服务器？”。在同一网络中的任何服务器都会回复并提供连接信息。

Mirror提供了一个简单的网络发现实现，你可以直接在你的游戏中使用。它还提供了一种扩展的方式，让你在发现阶段传递额外的数据。

![](<../../.gitbook/assets/image (97).png>)

包含了NetworkDiscovery和NetworkDiscoveryHUD组件，或者你可以根据[ScriptTemplate](../general/script-templates.md)自己创建。

网络发现使用LAN上的UDP广播，使客户端能够找到正在运行的服务器并连接到它。

当服务器启动时，它会在UDP广播监听端口上监听来自客户端的请求，并返回一个连接URI，客户端会将其应用到它们的传输中。

你可以通过Active Discovery Interval参数来调整客户端多久发送一次请求以查找服务器的时间间隔（以秒为单位）。

必须将Server Found事件分配给一个处理方法，例如NetworkDiscoveryHUD的OnDiscoveredServer方法。

在NetworkDiscoveryHUD中，NetworkDiscovery组件应该会被自动分配。

## 快速开始 <a href="#quick-start" id="quick-start"></a>

要使用网络发现，请按照以下步骤进行：

1. 如果尚未这样做，请创建一个带有NetworkManager的游戏对象
2. 不要添加NetworkManagerHUD。发现有一个不同的UI组件。
3. 将NetworkDiscoveryHUD组件添加到NetworkManager游戏对象中。\
   &#x20;NetworkDiscovery组件将自动添加并连接到你的HUD。
4. 如果尚未这样做，请向NetworkManager添加一个玩家。
5. 构建并运行一个独立版本
6. 点击“Start Host”
7. 在编辑器中启动播放模式，然后点击“Find Servers”
8. 编辑器应该会找到独立版本并显示一个按钮
9. 点击按钮连接到它。

NetworkDiscoveryHUD被提供为一种简单快速的入门方式，但您可能希望用自己的用户界面替换它。

## 自定义网络发现 <a href="#custom-network-discovery" id="custom-network-discovery"></a>

您可以通过添加自己的界面（通常基于Unity UI）来完全替换用户界面，而不是使用默认的NetworkDiscoveryHUD。您仍然需要NetworkDiscovery组件来完成繁重的工作。

有时，您可能希望在发现消息中提供更多信息。一些用例可能包括：

* 客户端可以显示服务器是否处于PvP或PvE模式
* 客户端可以显示服务器有多满
* 客户端可以显示到每个服务器的ping，以便玩家可以选择最快的服务器
* 客户端可以显示语言
* 客户端可以显示服务器是否受密码保护

为此，我们提供了一个[模板](../general/script-templates.md)，因此从Assets菜单中，单击Create > Mirror > Network Discovery。

这将在您的项目中创建一个脚本，其中包含2个空消息类和一个自定义NetworkDiscovery类，该类继承自NetworkDiscoveryBase，并包含了所有覆盖方法，并为您记录了这些方法。

消息类定义了客户端和服务器之间发送的内容。只要保持消息简单，使用Mirror可以序列化的[数据类型](../guides/data-types.md)，您就不需要为它们编写自定义序列化程序。

```csharp
public class DiscoveryRequest : NetworkMessage
{
    public string language="en";

    // Add properties for whatever information you want sent by clients
    // in their broadcast messages that servers will consume.
}

public class DiscoveryResponse : NetworkMessage
{
    enum GameMode {PvP, PvE};

    // you probably want uri so clients know how to connect to the server
    public Uri uri;

    public GameMode GameMode;
    public int TotalPlayers;
    public int HostPlayerName;

    // Add properties for whatever information you want the server to return to
    // clients for them to display or consume for establishing a connection.
}
```

自定义NetworkDiscovery类包含了处理上述消息的覆盖方法。

您可能希望参考Components/Discovery文件夹中的NetworkDiscovery.cs脚本，以了解应该如何实现这些方法。

```csharp
public class NewNetworkDiscovery: NetworkDiscoveryBase 
{
    #region Server

    protected override void ProcessClientRequest(DiscoveryRequest request, IPEndPoint endpoint)
    {
        base.ProcessClientRequest(request, endpoint);
    }

    protected override DiscoveryResponse ProcessRequest(DiscoveryRequest request, IPEndPoint endpoint) 
    {
        // TODO: Create your response and return it   
        return new DiscoveryResponse();
    }

    #endregion

    #region Client

    protected override DiscoveryRequest GetRequest()
    {
        return new DiscoveryRequest();
    }

    protected override void ProcessResponse(DiscoveryResponse response, IPEndPoint endpoint)
    {
        // TODO: a server replied,  do something with the response such as invoking a unityevent
    }

    #endregion
}
```

{% hint style="info" %}
注意：请考虑任何防病毒软件/防火墙阻止，以及特定平台的其他设置（iOS广播地址，Windows中Network Discovery共享设置等）。
{% endhint %}
