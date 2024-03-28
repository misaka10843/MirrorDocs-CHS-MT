---
description: 由 JesusLuvsYooh / StephenAllenGames.co.uk 撰写

# Ready Up And Die! (准备并死亡!)

## End Result: (最终结果:)

1: 创建一个“准备就绪”功能，这将阻止玩家移动，直到服务器/主机说开始。

2: 一个“isDead”玩家状态来创建死亡和重生。

![](../.gitbook/assets/RUAD9.jpg)

## **Part 1: (部分 1:)**

空项目，从 Asset Store/ Discord Releases Unity 包中导入 Mirror。

打开你的场景，本指南将使用 Mirror/Examples/Tanks。

你应该熟悉这些示例，如果不熟悉，请快速玩一下，构建并运行，通过“localhost”连接在同一台 PC 上拥有两个游戏来加入自己。

![Mirror/Examples/Tank/Scenes/Scene](../.gitbook/assets/Canvas2.jpg)

## **Part 2: (部分 2:)**

创建一个名为 SceneScript 的脚本，由于本指南修改了 Mirror 示例场景，确保它与 Tank 脚本在同一个文件夹中。

将此脚本添加到场景中的一个游戏对象上，也命名为 SceneScript，这将是我们与玩家链接的画布，同时也是我们的“场景管理器”，然后附加一个 NetworkIdentity 组件，如下所示。

![](../.gitbook/assets/RUAD3.jpg)

![](../.gitbook/assets/RUAD4.jpg)

![](../.gitbook/assets/RUAD5.jpg)

## **Part 3: (部分 3:)**

打开这个 SceneScript.cs，并添加以下代码，部分将被注释以解释 :)

{% code title="SceneScript.cs" %}
```csharp
using UnityEngine;
using UnityEngine.UI;
using Mirror;

// For your game, no need to use this namespace, its just for tank example modification  :)
namespace Mirror.Examples.Tanks
{

    public class SceneScript : NetworkBehaviour
    {
        [SyncVar(hook = nameof(OnReadyChanged))]
        public int readyStatus = 0;

        void OnReadyChanged(int _Old, int _New)
        {
            //this hook is fired when readyStatus is changed via client cmd or directly by host
            // you dont need to use _New, as the variable readyStatus is changed before hook is called

            if (readyStatus == 1)
            {
                statusText.text = "Server Says Go!";
                buttonReady.gameObject.SetActive(false);
            }
            // updating our canvas UI
            SetupScene();
        }

        public Tank playerScript; // this is set by local player, so UI has reference to your player only
        public Text statusText;
        public Button buttonReady, buttonDeath, buttonRespawn;
        

        private void Start()
        {
            //Make sure to attach these Buttons in the Inspector
            buttonReady.onClick.AddListener(ButtonReady);
            //you could choose to fully hide the server only buttons from clients, but for this guide we will show them to have less code involved
            buttonDeath.onClick.AddListener(ButtonDeath);
            buttonRespawn.onClick.AddListener(ButtonRespawn);
        }

        //[ServerCallback]
        public void ButtonReady()
        {
            // you can use the [ServerCallback] tag if server is only ever going to use the function, or do a check inside for  if( isServer ) { }
            if (isServer)
            {
                // optional checks to wait for full team, you can add this in connection joining callbacks, or via UI, upto you.
                //if (NetworkServer.connections.Count > 2)
                //{
                    readyStatus = 1;
                //}
                //else
                //{
                //    playerStatusText.text = "Not enough players.";
                //}
            }
            else
            {
                statusText.text = "Server only feature";
            }
        }

        // For faster prototyping, we will have these as buttons, but eventually they will be in your raycast, or trigger code
        public void ButtonDeath()
        {
            playerScript.CmdPlayerStatus(true);
        }

        public void ButtonRespawn()
        {
            playerScript.CmdPlayerStatus(false);
        }

        public void SetupScene()
        {
            if (isServer == false)
            {
                buttonReady.interactable = false;
            }

            if (readyStatus == 0)
            {
                buttonRespawn.interactable = false;
                buttonDeath.interactable = false;
            }
            else if (playerScript)
            {
                // quick check to make sure playerScript is set before checking its variables to prevent errors
                if (playerScript.isDead == true)
                {
                    buttonRespawn.interactable = true;
                    buttonDeath.interactable = false;
                }
                else if (playerScript.isDead == false)
                {
                    buttonRespawn.interactable = false;
                    buttonDeath.interactable = true;
                }
            }
        }
     }
}
```
{% endcode %}

## **Part 4: (部分 4:)**

现在打开 Tank.cs，这是指南中相当于你最终的 PlayerScript。

在 Update 函数中，在 isLocalPlayerCheck 下方添加:\
&#x20;if (sceneScript.readyStatus != 1) return;

这将阻止坦克下方的代码（移动和发射项目）在准备就绪之前运行。

![](../.gitbook/assets/RUAD6.jpg)

在 Tank.cs 的底部添加代码，放在“RpcOnFire”结束之后。

(但仍在 namespace { } 内)

{% code title="Tank" %}
```csharp
public GameObject[] objectsToHide;
private SceneScript sceneScript;

[SyncVar(hook = nameof(OnChanged))]
public bool isDead = false;

void OnChanged(bool _Old, bool _New)
{
    if (isDead == false) // respawn
    {
        // allow user to kill themselves via button, just for prototyping
        foreach (var obj in objectsToHide)
        {
            obj.SetActive(true);
        }

        if (isLocalPlayer)
        {
            // Uses NetworkStartPosition feature, optional.
            this.transform.position = NetworkManager.startPositions[Random.Range(0, NetworkManager.startPositions.Count)].position;
        }

        sceneScript.statusText.text = "Player Respawned";
    }
    else if (isDead == true) // death
    {
        // have meshes hidden, disable movement and show respawn button
        foreach (var obj in objectsToHide)
        {
            obj.SetActive(false);
        }

        sceneScript.statusText.text = "Player Defeated";
    }

    if (isLocalPlayer)
    {
        sceneScript.SetupScene();
    }
}

void Awake()
{
    //allow all players to run this, maybe they will need references to it in the future
    sceneScript = GameObject.FindObjectOfType<SceneScript>();
}

public override void OnStartLocalPlayer()
{
    // local player sets reference to scene scripts variable, so they can communicate with each other
    // you could also use regular Start() and if( isLocalPlayer ) { } instead of  OnStartLocalPlayer()
    sceneScript.playerScript = this;
    sceneScript.SetupScene();
}

[Command]
public void CmdPlayerStatus(bool _value)
{
    // player info sent to server, then server changes sync var which updates, causing hooks to fire
    isDead = _value;
}
```
{% endcode %}

## **Part 5: (部分 5:)**

对于这种死亡和重生的方法，而不是销毁玩家，我们将隐藏并重新显示它。

打开 Tank 玩家预制件，并将两个子对象添加到“Objects To Hide”数组中，“ProjectileMount”只是一个空的位置标记，不需要添加。

## **Part 6:** (Part 6:)

在“isDead”同步变量更改时，hook回调将循环遍历此数组。

![](../.gitbook/assets/RUAD7.jpg)

创建一个Canvas在场景中，可以通过右键单击，UI Canvas，或者在顶部菜单中选择GameObject，UI，Canvas。\
将Canvas缩放器设置为“Scale with Screen Size”，这将有助于保持所有内容在低分辨率和高分辨率屏幕上大小相同，并且最好在添加Canvas内容之前设置。

![](../.gitbook/assets/Canvas4.jpg)

创建您的Unity Canvas UI，包括一个文本和3个按钮，为了更容易引用，给它们标签。

在这里，我将状态文本放在顶部，将服务器设置准备按钮放在中间，将死亡和重生按钮放在右侧。\
之后，将变量设置为正确的UI在您的SceneScript中，如下所示。

![](../.gitbook/assets/RUAD8.jpg)

## **Part 7:** (Part 7:)

构建并运行！

享受  :)

（为了帮助进行并行测试 - 项目设置，玩家，分辨率和演示，将全屏模式设置为“窗口化”。）

![](../.gitbook/assets/RUAD9.jpg)
