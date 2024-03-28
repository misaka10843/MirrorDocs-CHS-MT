---
description: 由 JesusLuvsYooh / StephenAllenGames.co.uk 撰写，由 James Frowen 编辑
---

# Mirror 快速入门项目 (Quick Start Project)

本指南目前向您展示：

* [基本场景设置](quick-start-guide.md#part-1) (Basic scene setup)
* [玩家移动](quick-start-guide.md#part-4) (Player movement)
* [名称和颜色](quick-start-guide.md#part-8) (Names and colours)
* [带有画布按钮的场景脚本](quick-start-guide.md#part-11) (Scene script with canvas buttons)
* [武器切换](quick-start-guide.md#part-12) (Weapon switching)
* [网络场景对象调整](quick-start-guide.md#part-15) (Networked scene objects tweak)
* [菜单和场景切换](quick-start-guide.md#part-16) (Menu and scene switching)
* [武器射击](quick-start-guide.md#part-20) (Weapon firing)

最好先制作一个迷你练习游戏，然后再转换您的单人游戏，或创建您理想的全新多人游戏。

预制的 Mirror 示例非常适合用作参考，建议在连接设置方面使用它们，包括端口和防火墙。这可能是一个涉及个人之间巨大变化的主题，在本指南中未涵盖，这里我们将使用本地主机 (同一台 PC 上的多个游戏)。\
\
**最终结果:**

![](../.gitbook/assets/QS-image--036.jpg)

### Part 1 <a href="#part-1" id="part-1"></a>

空白项目，从 [Asset Store](https://assetstore.unity.com/packages/tools/network/mirror-129321) 导入 Mirror。

### Part 2 <a href="#part-2" id="part-2"></a>

* 创建新场景，保存并将其添加到构建设置中
* 在场景中创建一个新的 GameObject，命名为 NetworkManager，并添加以下 3 个组件
  * NetworkManager
  * KCPTransport (TelepathyTransport 已过时，您不需要 KCP 和 Telepathy)
  * NetworkManagerHUD
* 在 NetworkManager 组件上，将您的离线和在线场景拖放到插槽中，目前我们只有一个场景，所以将您的场景放在两个插槽中
  * 在将场景拖放到字段之前，该场景必须在构建设置中

![注意: KCP Transport 已在较新版本中取代了 Telepathy](../.gitbook/assets/QS-image--000.jpg)

![](../.gitbook/assets/QS-image--001.jpg)

### Part 3 <a href="#part-3" id="part-3"></a>

设置场景

* 添加一个简单的平面地板，具有:
  * 位置 (0, -1, 0)
  * 缩放 (2, 2, 2)
* (可选) 为此添加一个材质，我添加了一个名为 dirt 的材质，这是 Mirror 示例中使用的一个
* 接下来我们添加一个 GameObject，名称无关紧要
* 为此 GameObject 添加 `NetworkStartPosition` 组件
* 复制 GameObject 几次，并在场景地板周围散布，以便您有多个生成点。我做了 4 个，一个靠近每个角落

### Part 4 <a href="#part-4" id="part-4"></a> (Part 4)

创建玩家

* 使用菜单创建一个胶囊体，如图所示
* 附加一个 NetworkTransform 组件，这将自动添加一个 Network Identity
* 在 NetworkTransform 上勾选 Client Authority（客户端权限）

（注意：更新的 Mirror 版本使用 "Sync Direction"，将其设置为 "Client To Server"。）

* 将该对象重命名为 Player
* 添加一个空的 PlayerScript（玩家脚本）
* 将其拖入项目中以创建一个预制体
* 然后从场景中删除 Player

* 将玩家预制体拖入 Network Manager 中，
* 将生成方法设置为 Round Robin（轮询方式）。

### Part 5 <a href="#part-5" id="part-5"></a> (Part 5)

将以下内容添加到你的 PlayerScript 中。

```csharp
using Mirror;
using UnityEngine;

namespace QuickStart
{
    public class PlayerScript : NetworkBehaviour
    {
        public override void OnStartLocalPlayer()
        {
            Camera.main.transform.SetParent(transform);
            Camera.main.transform.localPosition = new Vector3(0, 0, 0);
        }

        void Update()
        {
            if (!isLocalPlayer) { return; }

            float moveX = Input.GetAxis("Horizontal") * Time.deltaTime * 110.0f;
            float moveZ = Input.GetAxis("Vertical") * Time.deltaTime * 4f;

            transform.Rotate(0, moveX, 0);
            transform.Translate(0, 0, moveZ);
        }
    }
}
```

### Part 6 <a href="#part-6" id="part-6"></a> (Part 6)

在 Unity 编辑器中按下播放按钮，然后在游戏窗口中点击 Host（服务器 + 客户端）按钮。你应该能够使用第一人称视角的胶囊体四处移动。

### Part 7 <a href="#part-7" id="part-7"></a> (Part 7)

构建并运行你的场景，打开它，在一个上托管，然后在另一个上点击客户端按钮。恭喜你，你制作了一个迷你多人游戏！

### Part 8 <a href="#part-8" id="part-8"></a> (Part 8)

在头顶显示玩家名称

* 在你的玩家预制体中，创建一个空的 GameObject
* 将其命名为类似 `FloatingInfo` 的内容
  * 将 Y 位置设置为 1.5
  * 将 X 缩放设置为 -1
* 在 `FloatingInfo` 中，使用 Unity 菜单创建一个 3D 文本（GameObject - 3D Object - 3D Text），
* 根据下面的图片设置它

### Part 9 <a href="#part-9" id="part-9"></a> (Part 9)

使用以下内容更新你的 PlayerScript.cs：

```csharp
using Mirror;
using UnityEngine;

namespace QuickStart
{
    public class PlayerScript : NetworkBehaviour
    {
        public TextMesh playerNameText;
        public GameObject floatingInfo;

        private Material playerMaterialClone;

        [SyncVar(hook = nameof(OnNameChanged))]
        public string playerName;

        [SyncVar(hook = nameof(OnColorChanged))]
        public Color playerColor = Color.white;

        void OnNameChanged(string _Old, string _New)
        {
            playerNameText.text = playerName;
        }

        void OnColorChanged(Color _Old, Color _New)
        {
            playerNameText.color = _New;
            playerMaterialClone = new Material(GetComponent<Renderer>().material);
            playerMaterialClone.color = _New;
            GetComponent<Renderer>().material = playerMaterialClone;
        }

        public override void OnStartLocalPlayer()
        {
            Camera.main.transform.SetParent(transform);
            Camera.main.transform.localPosition = new Vector3(0, 0, 0);
            
            floatingInfo.transform.localPosition = new Vector3(0, -0.3f, 0.6f);
            floatingInfo.transform.localScale = new Vector3(0.1f, 0.1f, 0.1f);

            string name = "Player" + Random.Range(100, 999);
            Color color = new Color(Random.Range(0f, 1f), Random.Range(0f, 1f), Random.Range(0f, 1f));
            CmdSetupPlayer(name, color);
        }

        [Command]
        public void CmdSetupPlayer(string _name, Color _col)
        {
            // player info sent to server, then server updates sync vars which handles it on all clients
            playerName = _name;
            playerColor = _col;
        }

        void Update()
        {
            if (!isLocalPlayer)
            {
                // make non-local players run this
                floatingInfo.transform.LookAt(Camera.main.transform);
                return;
            }

            float moveX = Input.GetAxis("Horizontal") * Time.deltaTime * 110.0f;
            float moveZ = Input.GetAxis("Vertical") * Time.deltaTime * 4f;

            transform.Rotate(0, moveX, 0);
            transform.Translate(0, 0, moveZ);
        }
    }
}
```

### Part 10 <a href="#part-10" id="part-10"></a> (Part 10)

将 `PlayerNameText` 和 `FloatingInfo` 对象添加到玩家预制体上的脚本中，如下所示。

现在，如果你构建并运行，在一个上托管，在另一个上加入，你将看到玩家名称和颜色在网络中同步！

### Part 11 <a href="#part-11" id="part-11">(Part 11)</a>

一个场景网络对象，所有人都可以访问和调整。

在场景中创建一个 SceneScript.cs，将其添加到名为 SceneScript 的空 GameObject 上。

然后创建一个 Canvas，包含文本和按钮，类似于下面的示例。

![](../.gitbook/assets/QS-image--011.jpg)

将 sceneScript 变量、Awake 函数和 CmdSendPlayerMessage 添加到 PlayerScript.cs 中，还要将新的 playerName joined 行添加到 CmdSetupPlayer() 中；

```csharp
private SceneScript sceneScript;

void Awake()
{
    //allow all players to run this
    sceneScript = GameObject.FindObjectOfType<SceneScript>();
}

[Command]
public void CmdSendPlayerMessage()
{
    if (sceneScript) 
        sceneScript.statusText = $"{playerName} says hello {Random.Range(10, 99)}";
}

[Command]
public void CmdSetupPlayer(string _name, Color _col)
{
    //player info sent to server, then server updates sync vars which handles it on all clients
    playerName = _name;
    playerColor = _col;
    sceneScript.statusText = $"{playerName} joined.";
}

public override void OnStartLocalPlayer()
{
    sceneScript.playerScript = this;
    //. . . . ^ new line to add here
```

将以下代码添加到 SceneScript.cs 中：

```csharp
using Mirror;
using UnityEngine;
using UnityEngine.UI;

namespace QuickStart
{
    public class SceneScript : NetworkBehaviour
    {
        public Text canvasStatusText;
        public PlayerScript playerScript;

        [SyncVar(hook = nameof(OnStatusTextChanged))]
        public string statusText;

        void OnStatusTextChanged(string _Old, string _New)
        {
            //called from sync var hook, to update info on screen for all players
            canvasStatusText.text = statusText;
        }

        public void ButtonSendMessage()
        {
            if (playerScript != null)  
                playerScript.CmdSendPlayerMessage();
        }
    }
}
```

* 将 ButtonSendMessage 函数附加到 Canvas 按钮上。
* 将 Canvas Scene Text 附加到 SceneScript 变量上。
  * 忽略 SceneScript 的 playerScript 变量，它会自动设置！
* 如果尚未自动执行，请将 NetworkIdentity 组件附加到 SceneScript GameObject 上。

![](https://mirror-networking.com/docs/Articles/CommunityGuides/MirrorQuickStartGuide/image--012.jpg) ![](../.gitbook/assets/QS-image--012.jpg) ![](../.gitbook/assets/image--013.jpg)

现在，如果构建并运行，主机和加入，您可以发送消息，并且有一个用于操作的文本日志！

哇哦！

![](../.gitbook/assets/image--014.jpg) ![](../.gitbook/assets/image--015.jpg)

尝试并调整，玩得开心！

![](../.gitbook/assets/QS-image--016.jpg)

### Part 12 <a href="#part-12" id="part-12">(Part 12)</a>

武器切换！代码片段。

将以下内容添加到 PlayerScript.cs 中：

```csharp
private int selectedWeaponLocal = 1;
public GameObject[] weaponArray;

[SyncVar(hook = nameof(OnWeaponChanged))]
public int activeWeaponSynced = 1;

void OnWeaponChanged(int _Old, int _New)
{
    // disable old weapon
    // in range and not null
    if (0 < _Old && _Old < weaponArray.Length && weaponArray[_Old] != null)
        weaponArray[_Old].SetActive(false);
    
    // enable new weapon
    // in range and not null
    if (0 < _New && _New < weaponArray.Length && weaponArray[_New] != null)
        weaponArray[_New].SetActive(true);
}

[Command]
public void CmdChangeActiveWeapon(int newIndex)
{
    activeWeaponSynced = newIndex;
}

void Awake() 
{
    // disable all weapons
    foreach (var item in weaponArray)
        if (item != null)
            item.SetActive(false); 
}
```

在更新中添加武器切换按钮。只有本地玩家才能切换自己的武器，因此它应该放在 `!isLocalPlayer` 检查之下。

```csharp
void Update()
{
    if (!isLocalPlayer)
    {
        // make non-local players run this
        floatingInfo.transform.LookAt(Camera.main.transform);
        return;
    }

    float moveX = Input.GetAxis("Horizontal") * Time.deltaTime * 110.0f;
    float moveZ = Input.GetAxis("Vertical") * Time.deltaTime * 4f;

    transform.Rotate(0, moveX, 0);
    transform.Translate(0, 0, moveZ);

    if (Input.GetButtonDown("Fire2")) //Fire2 is mouse 2nd click and left alt
    {
        selectedWeaponLocal += 1;

        if (selectedWeaponLocal > weaponArray.Length) 
            selectedWeaponLocal = 1; 

        CmdChangeActiveWeapon(selectedWeaponLocal);
    }
}
```

### Part 13 <a href="#part-13" id="part-13">(Part 13)</a>

武器模型

首先添加基本的立方体武器，稍后再更改这些。

* 双击您的玩家预制件以进入其中
* 添加一个名为 "WeaponsHolder" 的空 GameObject，位置和旋转均为 0,0,0。
* 在该 GameObject 内部，从 Unity 菜单创建一个立方体（GameObject, 3D object, cube）- 移除盒碰撞器。
* 将其重命名为 `Weapon1`，更改位置和比例以匹配下面的图片。

![](../.gitbook/assets/QS-image--017.jpg)

复制武器1以创建武器2，并更改其比例和位置，现在你应该有两种外观不同的“武器”！

![](../.gitbook/assets/QS-image--018.jpg)

### Part 14 <a href="#part-14" id="part-14"></a> (Part 14)

武器切换的最后部分。

* 将这两个 GameObject 添加到你的PlayerScript.cs（玩家脚本）武器数组中。
* 禁用武器2，这样只有武器1在生成时显示。

![](../.gitbook/assets/QS-image--019.jpg)

构建并运行！

你应该看到每个玩家都在切换武器，无论你的玩家装备了什么，都会自动显示在新加入的玩家身上（同步变量和钩子魔法！）

![](../.gitbook/assets/QS-image--020.jpg)

### Part 15 <a href="#part-15" id="part-15"></a> (Part 15)

在这里，我们将进行一些小调整，因为使用 GameObject.Find() 可能无法保证找到网络标识场景对象。在下面的图片中，你可以看到我们的 NetworkIdentity 场景对象被禁用，因为它们在玩家处于“准备就绪”状态之前是被禁用的（通常在玩家生成时设置准备就绪状态）。

所以我们选择的解决方法是让我们的 GameObject.Find() 获取非网络化的场景对象，这些场景对象将具有那些 Network Identity 场景对象作为预设变量。

创建一个名为 SceneReference.cs 的新脚本，并添加这个变量。

```csharp
using UnityEngine;

namespace QuickStart
{
    public class SceneReference : MonoBehaviour
    {
        public SceneScript sceneScript;
    }
}
```

打开 SceneScript.cs 并添加以下变量。

```csharp
public SceneReference sceneReference;
```

现在在你的 Unity 场景中创建一个游戏对象，命名为 SceneReference，并添加新脚本。在两个 Scene 游戏对象上，将引用设置为彼此。这样 SceneReference 就可以与 SceneScript 交流，SceneScript 也可以与 SceneReference 交流。

![](../.gitbook/assets/QS-image--022.jpg)

打开 PlayerScript.cs 并将 Awake 函数覆盖为以下内容：

```csharp
void Awake()
{
	//allows all players to run this
	sceneScript = GameObject.Find(“SceneReference”).GetComponent<SceneReference>().sceneScript;
}
```

### Part 16 <a href="#part-16" id="part-16"></a> (Part 16)

菜单和场景切换，这里我们将从一个离线菜单，带有一个播放按钮，切换到一个游戏列表，带有一个返回按钮和主机/加入 HUD，再到你的在线地图，然后是主机切换到的第二个地图。

打开 SceneScript.cs 并添加以下函数。

```csharp
public void ButtonChangeScene()
{
    if (isServer)
    {
        Scene scene = SceneManager.GetActiveScene();
        if (scene.name == "MyScene")
            NetworkManager.singleton.ServerChangeScene("MyOtherScene");
        else
            NetworkManager.singleton.ServerChangeScene("MyScene");
    }
    else
        Debug.Log("You are not Host.");
}
```

![](../.gitbook/assets/QS-image--023.jpg)

复制之前的 Canvas 按钮，重命名并重新定位，然后设置 OnClick() 指向 SceneScript.ButtonChangeScene，就像图片中一样。

然后将你的 NetworkManager 拖放到项目中，将其制作成 Prefab，这样稍后我们所做的任何更改都会应用到它们所有。如果还没有的话，你可以将项目整理到文件夹中，一个用于脚本、预制体、场景、纹理等等。 :)

![](../.gitbook/assets/QS-image--024.jpg)

### 标题17 (# Part 17)

保存，然后复制你的 MyScene，重命名为 Menu、GamesList 和 MyOtherScene，然后将它们添加到构建设置中，其中 Menu 放在第一位。

![](../.gitbook/assets/QS-image--025.jpg)

打开 Menu 场景，移除生成点、SceneScript、SceneReference、Network Manager 和 Plane，使其看起来像下面这样。调整画布按钮以显示 Play，并将其居中。在这里你可以添加得分场景、联系部分、新闻等等。

创建一个 Menu.cs 脚本，将其添加到 Menu gameObject 上。

![](../.gitbook/assets/QS-image--026.jpg)

将代码添加到 Menu.cs 中，然后在按钮中，将 Menu gameObject 拖放到 On Click() 中，并设置为 Menu.LoadScene，就像图片中一样。

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

namespace QuickStart
{
    public class Menu : MonoBehaviour
    {
        public void LoadScene()
        {
            SceneManager.LoadScene("GamesList");
        }
    }
}
```

![](../.gitbook/assets/QS-image--027.jpg)

### 标题18 (# Part 18)

打开 GamesList 场景，类似于 Menu，但保留 NetworkManager 预制体。

创建一个 GamesList.cs，添加代码，并将其添加到场景中的 GamesList gameObject 上。调整画布按钮以显示 Menu（这是我们的返回按钮）。它应该看起来像下面的图片。

* 游戏列表是你可以添加服务器内容列表、匹配器，或者只是主机和加入按钮，类似于默认的 NetworkManagerHud，暂时保留这些。 :)

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

namespace QuickStart
{
    public class GamesList : MonoBehaviour
    {
        public void LoadScene()
        {
            SceneManager.LoadScene("Menu");
        }
    }
}
```

![](../.gitbook/assets/QS-image--028.jpg)

### 标题19 (# Part 19)

打开 MyOtherScene，这是我们的第二个地图。更改相机背景颜色和地板材质（或任何内容，只要你能看到这两个场景是不同的即可。总结一下，MyScene 是地图 1，MyOtherScene 是地图 2。

在你的 NetworkManager 预制体中的项目中（而不是场景中的那个），添加 Menu 到 Offline（离线），并将 MyScene 添加到 Online（在线）变量。这将更改所有 NetworkManager 预制体的这些设置。

构建并运行，点击 Menu 进入 GamesList，然后点击 Host（玩家1）。对于玩家2，点击 Menu 进入，然后在 GamesList 上进行客户端连接。

现在主机可以在地图1和地图2之间切换场景，如果有人断开连接或停止游戏，Menu 场景将加载以重新开始。整个过程可以整理一下，但应该为你的 Mirror 游戏提供一个良好的场景切换模板 :)

### Part 20 <a href="#part-20" id="part-20">第20部分（Part 20）</a>

在这里，我们将添加基本的武器射击，使用刚体预制体。通常最好使用射线投射来表示发射的对象，并将物理对象保留给像手榴弹和炮弹之类的东西。为了保持指南简单，本节也将缺乏许多安全性和防作弊技术，但无论如何，让我们开始吧！

双击 Player Prefab 打开它，创建空的游戏对象并将它们与武器的末端对齐，将它们添加为每个武器的子对象。一些武器可能是短手枪，其他的可能是长步枪，因此对象生成的位置将不同。

创建一个 Weapon.cs 脚本，将其添加到 Player Prefab 中的 Weapon1 和 Weapon 2 游戏对象中。

```csharp
using UnityEngine;

namespace QuickStart
{
    public class Weapon : MonoBehaviour
    {
        public float weaponSpeed = 15.0f;
        public float weaponLife = 3.0f;
        public float weaponCooldown = 1.0f;
        public int weaponAmmo = 15;

        public GameObject weaponBullet;
        public Transform weaponFirePosition;
    }
}
```

### Part 21 <a href="#part-21" id="part-21">第21部分（Part 21）</a>

现在回到你的场景，我们将制作两种子弹，在 Unity 菜单中，转到 GameObject，3D Object，Sphere。给这个球体添加刚体，将比例设置为 0.2, 0.2, 0.2，然后将其保存为项目中的 Prefab。 用一个立方体做同样的操作，这样你就有两种外观不同的子弹。

再次选择玩家预制体，在武器上设置武器脚本的变量。

### 第22部分 (#part-22)

在SceneScript.cs中，添加以下变量和函数。

```csharp
public Text canvasAmmoText;
    
public void UIAmmo(int _value)
{
    canvasAmmoText.text = "Ammo: " + _value;
}
```

进入MyScene（地图1）。复制Canvas StatusText，重命名为Ammo，然后将该Ammo文本UI拖动到SceneScript游戏对象中，canvasAmmoText变量。在MyScene（地图1）和MyOtherScene（地图2）上都执行此操作，因为我们尚未将我们的画布和场景脚本链接或预制，以便在每个地图上自动更新更改。

![](../.gitbook/assets/QS-image--035.jpg)

打开PlayerScript.cs，添加以下两个变量：

```csharp
private Weapon activeWeapon;
private float weaponCooldownTime;  
```

在‘OnWeaponChanged’函数中，使用新行更新它，应该如下所示。

```csharp
void OnWeaponChanged(int _Old, int _New)
{
    // disable old weapon
    // in range and not null
    if (0 < _Old && _Old < weaponArray.Length && weaponArray[_Old] != null)
        weaponArray[_Old].SetActive(false);
    
    // enable new weapon
    // in range and not null
    if (0 < _New && _New < weaponArray.Length && weaponArray[_New] != null)
    {
        weaponArray[_New].SetActive(true);
        activeWeapon = weaponArray[activeWeaponSynced].GetComponent<Weapon>();
        if (isLocalPlayer)
            sceneScript.UIAmmo(activeWeapon.weaponAmmo);
    }
}
```

在Awake()中，在最后添加以下内容：

```csharp
if (selectedWeaponLocal < weaponArray.Length && weaponArray[selectedWeaponLocal] != null)
{
    activeWeapon = weaponArray[selectedWeaponLocal].GetComponent<Weapon>();
    sceneScript.UIAmmo(activeWeapon.weaponAmmo);
}
```

在Update()中，在最后添加以下内容：

```csharp
if (Input.GetButtonDown("Fire1") ) //Fire1 is mouse 1st click
{
    if (activeWeapon && Time.time > weaponCooldownTime && activeWeapon.weaponAmmo > 0)
    {
        weaponCooldownTime = Time.time + activeWeapon.weaponCooldown;
        activeWeapon.weaponAmmo -= 1;
        sceneScript.UIAmmo(activeWeapon.weaponAmmo);
        CmdShootRay();
    }
}
```

在Update() {}函数结束后添加以下两个函数。

```csharp
[Command]
void CmdShootRay()
{
    RpcFireWeapon();
}

[ClientRpc]
void RpcFireWeapon()
{
    //bulletAudio.Play(); muzzleflash  etc
    GameObject bullet = Instantiate(activeWeapon.weaponBullet, activeWeapon.weaponFirePosition.position, activeWeapon.weaponFirePosition.rotation);
    bullet.GetComponent<Rigidbody>().velocity = bullet.transform.forward * activeWeapon.weaponSpeed;
    Destroy(bullet, activeWeapon.weaponLife);
}
```

构建并运行，您应该可以在所有玩家上以不同的速度和冷却时间进行射击 :)

![](../.gitbook/assets/QS-image--036.jpg)
