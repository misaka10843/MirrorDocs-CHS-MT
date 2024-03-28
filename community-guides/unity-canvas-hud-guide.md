---
description: 由 JesusLuvsYooh / StephenAllenGames.co.uk 撰写
---

# Unity 画布 HUD（Head-Up Display）

{% hint style="info" %}
注意：现在在 Mirror/Examples/Common/CanvasNetworkManagerHUD 中提供了一个预制的 UI 画布 prefab。
{% endhint %}

## 结果展示：

![一个 Unity 画布，覆盖了大部分 OnGUI NetworkManagerHUD 组件的功能。](../.gitbook/assets/Canvas0.jpg)

## 第一部分

空项目，从 Asset Store/ Discord Releases unity package 导入 Mirror。&#x20;

打开你的场景，本指南中我们将使用 Mirror/Examples/Tanks。

你应该熟悉这些示例和默认的 NetworkManager HUD，它们看起来像这样：

![OnGUI NetworkManagerHUD。](../.gitbook/assets/Canvas1.jpg)

![Mirror/Examples/Tank/Scenes/Scene](../.gitbook/assets/Canvas2.jpg)

## 第二部分

在场景中创建一个 Canvas，可以通过右键点击，UI Canvas，或者顶部菜单，GameObject，UI，Canvas 来创建。\
&#x20;将画布缩放器设置为“随屏幕大小缩放”，这将有助于保持所有内容在低分辨率和高分辨率屏幕上大小相同，并且最好在添加画布内容之前设置。

然后创建并将一个新脚本附加到画布上，我将其命名为 CanvasHUD。

![缩放。](../.gitbook/assets/Canvas4.jpg)

![脚本。](../.gitbook/assets/Canvas3.jpg)

## 第三部分

打开这个新脚本，并打开 Mirror 的 NetworkManagerHUD（供参考）。

将以下代码添加为 CanvasHUD 的起始模板。

{% code title="CanvasHUD.cs" %}
```csharp
using Mirror;
using UnityEngine;
using UnityEngine.UI;

public class CanvasHUD : MonoBehaviour
{
	public Button buttonHost;

	public void Start()
	{
		buttonHost.onClick.AddListener(ButtonHost);
  }

	public void ButtonHost()
	{
		NetworkManager.singleton.StartHost();
	}
}
```
{% endcode %}

在主 Canvas 中创建一个按钮，并将其拖放到 Canvas 的“ButtonHost”变量中。对于本指南，我们不会过多关注画布的布局和外观，但可以随意调整并将内容放置在你喜欢的位置 :)

![简单按钮。](../.gitbook/assets/Canvas5.jpg)

## 第四部分

测试！启动游戏，按下你自己的“Host Button”，游戏应该开始。

恭喜，这是使用 Unity 画布与 Mirror 结合的第一步，并从 NetworkManagerHUD OnGUI 进行升级。

![新画布按钮和旧的 OnGUI](../.gitbook/assets/Canvas6.jpg)


## Part 5 (第5部分)

如果你检查旧的HUD，可以总结为2部分。在连接之前的“开始”和在连接之后的“停止”。

在画布内创建2个UI面板，将它们重命名为Panel Start和Panel Stop，从Panel Stop中移除图像组件，这样我们可以区分它们。

将你的“Button Host”拖放到Panel Start中。

![连接之前。](../.gitbook/assets/Canvas7.jpg)

![连接之后。](../.gitbook/assets/Canvas8.jpg)

![总结部分。](../.gitbook/assets/Canvas9.jpg)

## Part 6 (第6部分)

将以下变量添加到你的CanvasHUD脚本中，这些变量涵盖了大部分所需内容。

{% code title="CanvasHUD.cs" %}
```csharp
public GameObject PanelStart;
public GameObject PanelStop;

public Button buttonHost, buttonServer, buttonClient, buttonStop;

public InputField inputFieldAddress;

public Text serverText;
public Text clientText;
```
{% endcode %}

接下来，添加更多UI！很激动人心吧！:D

暂时不用担心代码，查看下面的图像以了解所需内容。

在“Panel Start”内应该有3个按钮，inputField和可选的标题文本。\
Panel Stop应包含一个按钮和2个文本，您可以在之后添加、移除和调整内容，但现在请按照此指南进行操作，以确保一切匹配。

将所有新的UI拖放到CanvasHUD脚本变量中，如果你在操作时都很好地标记了它们，那么这将是一个更容易的任务。

![场景视图，层次结构布局和脚本变量。](../.gitbook/assets/Canvas10.jpg)

## Part 7 (第7部分)

现在是让所有内容运作的代码部分，各部分将有注释来解释。\
这就是全部，你现在已经制作了自己的Unity Canvas HUD UI，或者升级了OnGUI NetworkManagerHUD！:D

{% code title="CanvasHUD.cs" %}
```csharp

    private void Start()
    {
        //Update the canvas text if you have manually changed network managers address from the game object before starting the game scene
        if (NetworkManager.singleton.networkAddress != "localhost") { inputFieldAddress.text = NetworkManager.singleton.networkAddress; }

        //Adds a listener to the main input field and invokes a method when the value changes.
        inputFieldAddress.onValueChanged.AddListener(delegate { ValueChangeCheck(); });

        //Make sure to attach these Buttons in the Inspector
        buttonHost.onClick.AddListener(ButtonHost);
        buttonServer.onClick.AddListener(ButtonServer);
        buttonClient.onClick.AddListener(ButtonClient);
        buttonStop.onClick.AddListener(ButtonStop);

        //This updates the Unity canvas, we have to manually call it every change, unlike legacy OnGUI.
        SetupCanvas();
    }

    // Invoked when the value of the text field changes.
    public void ValueChangeCheck()
    {
        NetworkManager.singleton.networkAddress = inputFieldAddress.text;
    }

    public void ButtonHost()
    {
        NetworkManager.singleton.StartHost();
        SetupCanvas();
    }

    public void ButtonServer()
    {
        NetworkManager.singleton.StartServer();
        SetupCanvas();
    }

    public void ButtonClient()
    {
        NetworkManager.singleton.StartClient();
        SetupCanvas();
    }

    public void ButtonStop()
    {
        // stop host if host mode
        if (NetworkServer.active && NetworkClient.isConnected)
        {
            NetworkManager.singleton.StopHost();
        }
        // stop client if client-only
        else if (NetworkClient.isConnected)
        {
            NetworkManager.singleton.StopClient();
        }
        // stop server if server-only
        else if (NetworkServer.active)
        {
            NetworkManager.singleton.StopServer();
        }

        SetupCanvas();
    }

    public void SetupCanvas()
    {
        // Here we will dump majority of the canvas UI that may be changed.

        if (!NetworkClient.isConnected && !NetworkServer.active)
        {
            if (NetworkClient.active)
            {
                PanelStart.SetActive(false);
                PanelStop.SetActive(true);
                clientText.text = "Connecting to " + NetworkManager.singleton.networkAddress + "..";
            }
            else
            {
                PanelStart.SetActive(true);
                PanelStop.SetActive(false);
            }
        }
        else
        {
            PanelStart.SetActive(false);
            PanelStop.SetActive(true);

            // server / client status message
            if (NetworkServer.active)
            {
                serverText.text = "Server: active. Transport: " + Transport.active;
                // Note, older mirror versions use: Transport.activeTransport
            }
            if (NetworkClient.isConnected)
            {
                clientText.text = "Client: address=" + NetworkManager.singleton.networkAddress;
            }
        }
    }
```
{% endcode %}
