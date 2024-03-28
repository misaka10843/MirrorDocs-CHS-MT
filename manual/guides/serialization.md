# 序列化(Serialization)

这个页面深入探讨了序列化(Serialization)，有关基础知识请参见[数据类型(DataTypes)](data-types.md)。

Mirror使用Weaver为类型创建序列化(Serialize)和反序列化(Deserialize)函数。Weaver在Unity编译后使用Mono.Cecil编辑dll。这使得Mirror能够拥有许多复杂功能，如SyncVar(同步变量)、ClientRpc(客户端RPC)和消息序列化，而无需用户手动设置所有内容。

## 规则和提示 <a href="#rules-and-tips" id="rules-and-tips">(Rules and tips)</a>

Weaver能够执行的操作有一些规则和限制。一些功能会增加复杂性并且难以维护，因此尚未实现。这些功能并非不可能实现，如果有很高的需求，它们可以被添加进来。

* 您应该能够为任何类型编写自定义读取/写入函数，并且Weaver将使用它们。
  * 这意味着如果有一个不受支持的类型，比如`int[][]`，创建一个自定义读取/写入函数将允许您在SyncVar(同步变量)、ClientRpc(客户端RPC)等中同步`int[][]`。
* 如果您有一个类型，其中包含无法序列化的字段，您可以使用`[System.NonSerialized]`标记该字段，Weaver将忽略它。

### 不支持的类型 <a href="#unsupported-types" id="unsupported-types">(Unsupported Types)</a>

由于它们可能增加的复杂性，以下类型是不受支持的，如上所述。

> 注意：此列表中的类型可以有自定义的写入函数。

* 锯齿数组和多维数组
* 继承自`UnityEngine.Component`的类型
* `UnityEngine.Object`
* `UnityEngine.ScriptableObject`
* 泛型类型，例如`MyData`
  * 自定义读取/写入必须声明T，例如`MyData`
* 接口
* 引用自身的类型

### 内置读写函数 <a href="#built-in-read-write-functions" id="built-in-read-write-functions">(Built-in Read Write Functions)</a>

Mirror提供了一些内置的读写函数。它们可以在`NetworkReaderExtensions`和`NetworkWriterExtensions`中找到。

这是一个非竞争性列表，列出了具有内置函数的类型，查看上述类以查看完整列表。

* 大多数[C#基本类型](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/built-in-types)
* 常见的Unity结构体
  * Vector3
  * Quaternion
  * Rect
  * Ray
  * Guid
* NetworkIdentity、GameObject、Transform

### NetworkIdentity(网络标识), GameObject(游戏对象), Transform(变换)

对象的 `netId` 会通过网络发送，另一端会返回具有相同 `netId` 的对象。如果 `netId` 为零或未找到对象，则会返回 `null`。

### 生成的读写函数 <a href="#generated-read-write-functions" id="generated-read-write-functions"></a>

Weaver 会为以下内容生成读写函数：

* 类或结构体
* 枚举
* 数组
  * 例如 `int[]`
* ArraySegments(数组段)
  * 例如 `ArraySegment`
* 列表
  * 例如 `List`

#### 类和结构体 <a href="#classes-and-structs" id="classes-and-structs"></a>

Weaver 会读取/写入类型中的每个公共字段，除非该字段标记为 `[System.NonSerialized]`。如果类或结构体中存在不受支持的类型，则 Weaver 将无法为其生成读写函数。

> 注意：Weaver 不会检查属性

#### 枚举 <a href="#enums" id="enums"></a>

Weaver 将使用枚举的基础类型来读取和写入它们。默认情况下，这是 `int`。

例如 `Switch` 将使用 `byte` 读写函数进行序列化

```csharp
public enum Switch : byte
{
    Left,
    Middle,
    Right,
}
```

#### 集合 <a href="#collections" id="collections"></a>

Weaver 将为上述列出的集合生成写入函数。Weaver 将使用元素的读写函数。元素必须具有读写函数，因此必须是受支持的类型，或具有自定义读写函数。

例如：

* `float[]` 是受支持的类型，因为 Mirror 为 `float` 内置了读写函数。
* `MyData[]` 是受支持的类型，因为 Weaver 能够为 `MyData` 生成读写函数

```csharp
public struct MyData
{
    public int someValue;
    public float anotherValue;
}
```

## 添加自定义读写函数 <a href="#adding-custom-read-write-functions" id="adding-custom-read-write-functions"></a>

读写函数是以下形式的静态方法：

```csharp
public static void WriteMyType(this NetworkWriter writer, MyType value)
{
    // write MyType data here
}

public static MyType ReadMyType(this NetworkReader reader)
{
    // read MyType data here
}
```

最佳实践是将读写函数制作为[扩展方法](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)，这样它们可以像 `writer.WriteMyType(value)` 这样调用。

这是一个好主意，将它们称为`ReadMyType`(读取我的类型)和`WriteMyType`(写入我的类型)，这样很明显知道它们是用于什么类型的。然而函数的名称并不重要，编织器应该能够找到它，无论它被称为什么。

#### 属性示例 <a href="#properties-example" id="properties-example"></a>

编织器不会写入属性，但可以使用自定义编写器将它们发送到网络上。

如果您希望为属性设置私有设置，这可能很有用

```csharp
public struct MyData
{
    public int someValue { get; private set; }
    public float anotherValue { get; private set; }

    public MyData(int someValue, float anotherValue)
    {
        this.someValue = someValue;
        this.anotherValue = anotherValue;
    }
}

public static class CustomReadWriteFunctions 
{
    public static void WriteMyType(this NetworkWriter writer, MyData value)
    {
        writer.WriteInt32(value.someValue);
        writer.WriteSingle(value.anotherValue);
    }

    public static MyData ReadMyType(this NetworkReader reader)
    {
        return new MyData(reader.ReadInt32(), reader.ReadSingle());
    }
}
```

#### 不支持的类型示例 <a href="#unsupported-type-example" id="unsupported-type-example"></a>

Rigidbody(刚体)是一个不支持的类型，因为它继承自`Component`(组件)。但是可以添加自定义编写器，以便在附加了NetworkIdentity(网络标识)的情况下进行同步。

```csharp
public struct MyCollision
{
    public Vector3 force;
    public Rigidbody rigidbody;
}

public static class CustomReadWriteFunctions
{
    public static void WriteMyCollision(this NetworkWriter writer, MyCollision value)
    {
        writer.WriteVector3(value.force);

        NetworkIdentity networkIdentity = value.rigidbody.GetComponent<NetworkIdentity>();
        writer.WriteNetworkIdentity(networkIdentity);
    }

    public static MyCollision ReadMyCollision(this NetworkReader reader)
    {
        Vector3 force = reader.ReadVector3();

        NetworkIdentity networkIdentity = reader.ReadNetworkIdentity<NetworkIdentity>();
        Rigidbody rigidBody = networkIdentity != null
            ? networkIdentity.GetComponent()
            : null;

        return new MyCollision
        {
            force = force,
            rigidbody = rigidBody,
        };
    }
}
```

上面是用于`MyCollision`(我的碰撞)的函数，但您也可以为`Rigidbody`(刚体)添加函数，让编织器为`MyCollision`(我的碰撞)生成一个编写器。

```csharp
public static class CustomReadWriteFunctions
{
    public static void WriteRigidbody(this NetworkWriter writer, Rigidbody rigidbody)
    {
        NetworkIdentity networkIdentity = rigidbody.GetComponent<NetworkIdentity>();
        writer.WriteNetworkIdentity(networkIdentity);
    }

    public static Rigidbody ReadRigidbody(this NetworkReader reader)
    {
        NetworkIdentity networkIdentity = reader.ReadNetworkIdentity();
        Rigidbody rigidBody = networkIdentity != null
            ? networkIdentity.GetComponent<Rigidbody>()
            : null;

        return rigidBody;
    }
}
```

## 调试 <a href="#debugging" id="debugging"></a>

您可以使用类似 [ILSpy](https://github.com/icsharpcode/ILSpy) 和 [dnSpy](https://github.com/0xd4d/dnSpy) 的工具，在编织器修改代码后查看编译后的代码。这有助于理解和调试 Mirror 和 Weaver 的操作。
