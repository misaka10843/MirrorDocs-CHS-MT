# 网络消息(Network Messages)

对于大部分情况，我们推荐使用高级的[Commands and RPC(远程操作)](remote-actions.md)调用和[SyncVar(同步变量)](../synchronization/syncvars.md)，但你也可以发送低级别的网络消息。这在你希望客户端发送与游戏对象无关的消息时很有用，比如日志、分析或性能分析信息。

有一个名为NetworkMessage的公共接口，你可以扩展它以创建可序列化的网络消息结构体。Mirror会自动生成网络消息的序列化和反序列化方法，并且它们可以高效处理任何[支持的Mirror类型](../data-types.md)，但如果需要的话，你也可以按照我们的[序列化指南](../serialization.md)实现自己的序列化方法。一些实现低级消息的其他有用提示：

* 确保所有成员都是公共字段。Weaver不会序列化属性。
* 如果需要类或复杂容器，比如`List<T>`和`Dictionary<T,K>`，你必须自己实现Read和Write方法。

要发送消息，可以使用NetworkClient、NetworkServer和NetworkConnection类上的`Send()`方法，它们都使用从NetworkMessage派生的消息结构体，工作方式相同。下面的代码演示了如何发送和处理消息：

要声明自定义网络消息类并使用它：

```csharp
using UnityEngine;
using Mirror;

public class Scores : MonoBehaviour
{
    public struct ScoreMessage : NetworkMessage
    {
        public int score;
        public Vector3 scorePos;
        public int lives;
    }

    public void SendScore(int score, Vector3 scorePos, int lives)
    {
        ScoreMessage msg = new ScoreMessage()
        {
            score = score,
            scorePos = scorePos,
            lives = lives
        };

        NetworkServer.SendToAll(msg);
    }

    public void SetupClient()
    {
        NetworkClient.RegisterHandler<ScoreMessage>(OnScore);
        NetworkClient.Connect("localhost");
    }

    public void OnScore(ScoreMessage msg)
    {
        Debug.Log("OnScoreMessage " + msg.score);
    }
}
```
