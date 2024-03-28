# 拾取、掉落和子对象（Pickups, Drops, and Child Objects）

经常会有关于如何处理作为玩家预制件的子对象附加的对象的问题，所有客户端都需要知道并同步，比如装备了哪种武器、拾取网络场景对象以及玩家将对象放入场景中。

{% hint style="warning" %}
Mirror 无法支持对象层次结构中存在多个网络身份（Network Identity）组件。由于玩家对象必须具有网络身份，因此其所有后代对象都不能具有网络身份。
{% endhint %}

## 子对象（Child Objects）<a href="#child-objects" id="child-objects"></a>

让我们从一个简单的情况开始，即一个单独的附着点，它位于我们的玩家层次结构的某个位置，比如手臂末端的手。在继承自玩家预制件上的 NetworkBehaviour 的脚本中，我们会有一个 `GameObject` 引用，可以在检视面板中分配附着点，一个包含各种玩家所持物品选择的 SyncVar 枚举，以及一个用于 SyncVar 的 Hook，根据新值替换所持物品的艺术品。

在下面的图片中，Kyle 有一个空的游戏对象 `RightHand`，添加到手腕上，并一些要装备的预制件（Ball、Box、Cylinder），以及一个处理它们的 Player Equip 脚本。

{% hint style="info" %}
**注意**：物品预制件仅为 _艺术品_...它们没有脚本，并且 _不能_ 具有网络组件。当然，它们可以有基于 MonoBehaviour 的脚本，可以从玩家预制件上的 ClientRpc 引用和调用。
{% endhint %}

检视面板显示了 `RightHand` 在 2 个位置分配，Player Equip 脚本，以及网络变换子组件的目标，因此我们可以根据需要调整附着点的相对位置（而不是艺术品）以适应所有客户端。

![](<../../../.gitbook/assets/image (16).png>)

以下是处理更换装备物品的 Player Equip 脚本，以及一些需要考虑的注意事项：

* 虽然我们可以在设计时将所有艺术品物品附加在一起，然后根据枚举启用/禁用它们，但这对于大量物品并且如果它们具有脚本来控制它们在游戏中的行为（如动画、特效等）来说并不是一个好的扩展方式，因此这个示例选择在本地实例化和销毁。
* 该示例没有努力处理物品与附着点之间的位置偏移，例如使物品的握把或手柄与手对齐。最好在物品上有一个 MonoBehaviour 脚本，其中有用于本地位置和旋转的公共字段，可以在设计时设置这些值，并在 Start 中应用这些值的一小段代码，以相对于父附着点的本地坐标。

```csharp
using UnityEngine;
using System.Collections;
using Mirror;

public enum EquippedItem : byte
{
    nothing,
    ball,
    box,
    cylinder
}

public class PlayerEquip : NetworkBehaviour
{
    public GameObject sceneObjectPrefab;

    public GameObject rightHand;

    public GameObject ballPrefab;
    public GameObject boxPrefab;
    public GameObject cylinderPrefab;

    [SyncVar(hook = nameof(OnChangeEquipment))]
    public EquippedItem equippedItem;

    void OnChangeEquipment(EquippedItem oldEquippedItem, EquippedItem newEquippedItem)
    {
        StartCoroutine(ChangeEquipment(newEquippedItem));
    }

    // Since Destroy is delayed to the end of the current frame, we use a coroutine
    // to clear out any child objects before instantiating the new one
    IEnumerator ChangeEquipment(EquippedItem newEquippedItem)
    {
        while (rightHand.transform.childCount > 0)
        {
            Destroy(rightHand.transform.GetChild(0).gameObject);
            yield return null;
        }

        switch (newEquippedItem)
        {
            case EquippedItem.ball:
                Instantiate(ballPrefab, rightHand.transform);
                break;
            case EquippedItem.box:
                Instantiate(boxPrefab, rightHand.transform);
                break;
            case EquippedItem.cylinder:
                Instantiate(cylinderPrefab, rightHand.transform);
                break;
        }
    }

    void Update()
    {
        if (!isLocalPlayer) return;

        if (Input.GetKeyDown(KeyCode.Alpha0) && equippedItem != EquippedItem.nothing)
            CmdChangeEquippedItem(EquippedItem.nothing);
        if (Input.GetKeyDown(KeyCode.Alpha1) && equippedItem != EquippedItem.ball)
            CmdChangeEquippedItem(EquippedItem.ball);
        if (Input.GetKeyDown(KeyCode.Alpha2) && equippedItem != EquippedItem.box)
            CmdChangeEquippedItem(EquippedItem.box);
        if (Input.GetKeyDown(KeyCode.Alpha3) && equippedItem != EquippedItem.cylinder)
            CmdChangeEquippedItem(EquippedItem.cylinder);
    }

    [Command]
    void CmdChangeEquippedItem(EquippedItem selectedItem)
    {
        equippedItem = selectedItem;
    }
}
```

## 丢弃物品 <a href="#dropping-items" id="dropping-items"></a>

现在我们可以装备物品了，我们需要一种方法将当前物品作为网络物品丢弃到世界中。请记住，作为子艺术品，物品预制件上根本没有任何网络组件。

首先，让我们在上面的Update方法中再添加一个Input，并添加一个`CmdDropItem`方法：

```csharp
    void Update()
    {
        if (!isLocalPlayer) return;

        if (Input.GetKeyDown(KeyCode.Alpha0) && equippedItem != EquippedItem.nothing)
            CmdChangeEquippedItem(EquippedItem.nothing);
        if (Input.GetKeyDown(KeyCode.Alpha1) && equippedItem != EquippedItem.ball)
            CmdChangeEquippedItem(EquippedItem.ball);
        if (Input.GetKeyDown(KeyCode.Alpha2) && equippedItem != EquippedItem.box)
            CmdChangeEquippedItem(EquippedItem.box);
        if (Input.GetKeyDown(KeyCode.Alpha3) && equippedItem != EquippedItem.cylinder)
            CmdChangeEquippedItem(EquippedItem.cylinder);

        if (Input.GetKeyDown(KeyCode.X) && equippedItem != EquippedItem.nothing)
            CmdDropItem();
    }
```

```csharp
    [Command]
    void CmdDropItem()
    {
        // Instantiate the scene object on the server
        Vector3 pos = rightHand.transform.position;
        Quaternion rot = rightHand.transform.rotation;
        GameObject newSceneObject = Instantiate(sceneObjectPrefab, pos, rot);

        // set the RigidBody as non-kinematic on the server only (isKinematic = true in prefab)
        newSceneObject.GetComponent<Rigidbody>().isKinematic = false;

        SceneObject sceneObject = newSceneObject.GetComponent<SceneObject>();

        // set the child object on the server
        sceneObject.SetEquippedItem(equippedItem);

        // set the SyncVar on the scene object for clients
        sceneObject.equippedItem = equippedItem;

        // set the player's SyncVar to nothing so clients will destroy the equipped child item
        equippedItem = EquippedItem.nothing;

        // Spawn the scene object on the network for all to see
        NetworkServer.Spawn(newSceneObject);
    }
```

在上面的图像中，有一个`sceneObjectPrefab`字段，它被分配给一个将作为我们物品预制件容器的预制件。SceneObject预制件具有一个类似于Player Equip脚本的SyncVar的SceneObject脚本，并且有一个接受共享枚举值作为参数的SetEquippedItem方法。

```csharp
using UnityEngine;
using System.Collections;
using Mirror;

public class SceneObject : NetworkBehaviour
{
    [SyncVar(hook = nameof(OnChangeEquipment))]
    public EquippedItem equippedItem;

    public GameObject ballPrefab;
    public GameObject boxPrefab;
    public GameObject cylinderPrefab;

    void OnChangeEquipment(EquippedItem oldEquippedItem, EquippedItem newEquippedItem)
    {
        StartCoroutine(ChangeEquipment(newEquippedItem));
    }

    // Since Destroy is delayed to the end of the current frame, we use a coroutine
    // to clear out any child objects before instantiating the new one
    IEnumerator ChangeEquipment(EquippedItem newEquippedItem)
    {
        while (transform.childCount > 0)
        {
            Destroy(transform.GetChild(0).gameObject);
            yield return null;
        }

        // Use the new value, not the SyncVar property value
        SetEquippedItem(newEquippedItem);
    }

    // SetEquippedItem is called on the client from OnChangeEquipment (above),
    // and on the server from CmdDropItem in the PlayerEquip script.
    public void SetEquippedItem(EquippedItem newEquippedItem)
    {
        switch (newEquippedItem)
        {
            case EquippedItem.ball:
                Instantiate(ballPrefab, transform);
                break;
            case EquippedItem.box:
                Instantiate(boxPrefab, transform);
                break;
            case EquippedItem.cylinder:
                Instantiate(cylinderPrefab, transform);
                break;
        }
    }
}
```

在下面的运行时图像中，Ball(Clone)附加到`RightHand`对象上，而Box(Clone)附加到SceneObject(Clone)上，这在检查器中显示。

{% hint style="info" %}
艺术预制件上有简单的碰撞器（球体、立方体、胶囊体）。如果您的艺术物品有一个网格碰撞器，它必须标记为Convex才能与SceneObject容器上的RigidBody一起使用。
{% endhint %}

![](<../../../.gitbook/assets/image (84).png>)

## 拾取物品 <a href="#pickup-items" id="pickup-items"></a>

现在我们在场景中放置了一个箱子，我们需要再次将其拾起。为此，在Player Equip脚本中添加了一个`CmdPickupItem`方法：

```csharp
    // CmdPickupItem is public because it's called from a script on the SceneObject
    [Command]
    public void CmdPickupItem(GameObject sceneObject)
    {
        // set the player's SyncVar so clients can show the equipped item
        equippedItem = sceneObject.GetComponent<SceneObject>().equippedItem;

        // Destroy the scene object
        NetworkServer.Destroy(sceneObject);
    }
```

这个方法只是从Scene Object脚本中的`OnMouseDown`中调用：

```csharp
    void OnMouseDown()
    {
        NetworkClient.localPlayer.GetComponent<PlayerEquip>().CmdPickupItem(gameObject);
    }
```

由于SceneObject(Clone)是网络化的，我们可以直接将其传递给玩家对象上的`CmdPickupItem`，以设置装备物品SyncVar并销毁场景对象。

在整个示例中，除了Player之外，唯一需要在Network Manager中注册的预制件是SceneObject预制件。
```
