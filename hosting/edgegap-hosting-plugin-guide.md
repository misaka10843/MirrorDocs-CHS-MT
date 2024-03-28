---
description: Mirror的非官方Edgegap Hosting插件文档。
---

# Edgegap Hosting 插件指南（Guide）

感谢**Edgegap**为 Unity 提供的 Hosting 插件，Mirror 用户可以免费获得 0.5 vCPU 的云托管！

您可以直接从 Unity 编辑器构建并启动游戏服务器到云端，完全不需要使用 Linux 或云端。

<figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
这是一个**早期版本**。

如果遇到问题，请在我们的 Discord 的**#edgegap**频道中报告。
{% endhint %}

## 概述（Overview）

设置仅需几分钟，包括 4 个基本步骤：

1. 创建一个**Edgegap.com**账户
2. 安装 Unity **Linux**构建支持和**Docker**桌面
3. 配置 Unity **插件**
4. **构建并推送**我们的服务器到 Edgegap

## 在 Unity / Mirror 中打开插件

Edgegap 的 Hosting 插件（版本 2）现在始终包含在最新的 Mirror 版本中。

它至少需要 Unity 2023 LTS，由于使用 UIToolkit，它不适用于旧版本。

原始源代码在 Github 上，但我们对 Mirror 中的版本应用了一些修复。

{% embed url="https://github.com/edgegap/edgegap-unity-plugin" %}

如果您使用的是较旧的 Mirror 版本，您也可以从我们的 Github 存储库手动下载 Assets/Mirror/Hosting 文件夹。如果出现有关缺少'Newtonsoft Json'的错误，请将此包添加到您的包管理器：

<figure><img src="../.gitbook/assets/2023-11-05 - 13-14-02@2x.png" alt=""><figcaption></figcaption></figure>

您还可以打开`Packages/manifest.json`文件并手动添加此行：

```json
"com.unity.nuget.newtonsoft-json": "3.2.1"
```

默认情况下，这应该可以直接使用。

一旦安装完成，请在 Unity 的顶部菜单中检查“**Edgegap** -> **Edgegap** Hosting”：

<figure><img src="../.gitbook/assets/2023-12-07 - 13-18-23@2x.png" alt=""><figcaption><p>Edgegap Hosting插件V3</p></figcaption></figure>

## 获取令牌（Get a Token）

下一步是从 Edgegap 网站获取一个**令牌**。

只需点击**获取令牌**按钮，然后要么登录到您现有的 Edgegap.com 账户，要么点击页面上的**开始**链接创建一个新账户。

<figure><img src="../.gitbook/assets/2023-12-07 - 13-21-42@2x.png" alt=""><figcaption></figcaption></figure>

确认您的电子邮件地址后，您将被要求命名**您的组织**。您可以输入任何内容，在这种情况下我们使用“**Your-Organization**”。

<figure><img src="../.gitbook/assets/2023-12-07 - 13-55-40@2x.png" alt=""><figcaption></figcaption></figure>

之后，您将被要求**开始应用程序导览**。现在跳过它。

接下来，您将自动看到**令牌创建成功**屏幕，因为我们最初点击了 Unity 插件中的**获取令牌**按钮：

<figure><img src="../.gitbook/assets/2023-12-07 - 13-58-35@2x.png" alt=""><figcaption></figcaption></figure>

点击**剪贴板**图标复制令牌，然后将其粘贴回 Unity 插件中并点击**验证**。\
_请注意，令牌看起来像“token 123456-abcdef-00000-...”。_\
_“token ...”部分需要保留在那里。_

<figure><img src="../.gitbook/assets/2023-12-07 - 13-59-44@2x.png" alt=""><figcaption></figcaption></figure>

验证成功后，在字段中输入一个**应用程序名称**，然后点击**创建应用程序**：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-02-43@2x.png" alt=""><figcaption></figcaption></figure>

它会显示**成功**，然后向您展示应用程序详细信息。

下次您可以输入您已创建的应用程序名称，然后点击**加载现有应用程序**。

最后的配置步骤：我们需要输入**端口**和**协议类型**。

在 Unity 中打开您的场景（例如我们的坦克演示），找到 NetworkManager，找到 Transport 组件，将端口复制到插件中。\
\=> 如果您正在使用`Kcp`，则**协议类型**是`UDP`。\
\=> 如果您正在使用`Telepathy`，则是`TCP`。

<figure><img src="../.gitbook/assets/2023-12-07 - 14-05-19@2x.png" alt=""><figcaption></figcaption></figure>

# 标题

**快要完成了！**\
在我们构建和推送之前，我们需要为 Unity 安装 Linux 构建支持和 Docker！

## 安装 Unity Linux 构建支持

在你的 **Unity Hub** 中，选择 **Installs**，点击你的 Unity 版本旁边的配置图标，然后点击 **Add Modules**：

<figure><img src="../.gitbook/assets/2023-11-03 - 18-52-53@2x.png" alt=""><figcaption></figcaption></figure>

找到并安装 **Linux 专用服务器构建**。为了安全起见，你也可以添加其他模块：

<figure><img src="../.gitbook/assets/2023-11-03 - 18-54-28@2x.png" alt=""><figcaption></figcaption></figure>

点击继续，等待，重新启动 Unity 一次，完成。

## 安装 Docker Desktop

**Edgegap** 使用容器，这意味着我们需要安装 Docker。我们不需要担心它，只需要安装一次即可。

最简单的方法就是安装 **Docker Desktop**：

{% embed url="https://www.docker.com/products/docker-desktop/" %}

下载安装，打开并保持运行。你可以设置让它随操作系统自动启动。

{% hint style="warning" %}
一些用户报告称他们在使用 Docker 前需要重新启动计算机一次。为了安全起见，考虑重新启动一次！
{% endhint %}

{% hint style="info" %}
如果你在意的话，这里有关于 Docker 的简要解释。你不需要了解这些，随意跳过。

基本上 Docker 是一种非常简单的方式来配置一个虚拟机用于你的游戏服务器构建。以前你需要在比如 Google Cloud 手动创建一个虚拟机，配置硬盘，打开端口，安装 Linux 版本，运行 apt-update，安装依赖项，...

使用 Docker，我们只需要一个文本文件。它说‘安装 ubuntu，将我们的构建复制到虚拟机中，导航到文件夹，运行 unity。再次强调，你不需要担心这些。插件会自动创建这些，类似于这样（再次强调，不需要担心）：

```
FROM ubuntu:bionic
ARG DEBIAN_FRONTEND=noninteractive
COPY Builds/EdgegapServer /root/build/
WORKDIR /root/
RUN chmod +x /root/build/ServerBuild
ENTRYPOINT [ "/root/build/ServerBuild", "-batchmode", "-nographics"]

```

{% endhint %}

接下来，我们需要登录到 Edgegap 的 Docker 注册表。

我们正在与 Edgegap 合作自动化这一过程。目前，你需要打开一个终端 / 控制台：

- 在 Windows 上，按下 CTRL+R，输入 CMD，按回车键打开它。
- 在 Mac 上，打开 Finder，转到 应用程序 -> 实用工具 -> 终端。
- 在 Linux 上，你可能知道如何操作。

{% hint style="info" %}
不要害怕终端。它只是一个黑色背景和白色文字的地方，我们将在其中输入一条命令。这很快就会自动化。
{% endhint %}

从 Edgegap.com 的**容器注册表**页面获取您的用户名 + 令牌：

[https://app.edgegap.com/registry-management/repositories/list](https://app.edgegap.com/registry-management/repositories/list)

您可以在**凭据:**下找到它们：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-19-45@2x.png" alt=""><figcaption></figcaption></figure>

\
现在输入这个简单的终端命令。不会有其他命令，承诺！

```
docker login registry.edgegap.com
```

然后输入您在 Edgegap.com -> **容器注册表**页面看到的用户名和令牌（作为密码）。

如果您似乎无法登录，也可以尝试使用以下命令：

```
docker login -u "YOUR_USERNAME" registry.edgegap.com
```

Docker Desktop 会记住您的登录信息，所以下次不必再次执行此操作。

好了，Docker 就介绍到这里。

## 构建和推送

在构建游戏服务器之前，请选择您的 `NetworkManager` 并确保 **自动启动服务器** 在 **无头模式** 中已启用。否则，服务器应用程序可能会启动，但不会调用 NetworkManager.StartServer()，因此没有人能够连接。

<figure><img src="../.gitbook/assets/2023-12-07 - 14-13-06@2x.png" alt=""><figcaption></figcaption></figure>

接下来，在 Unity 插件中按下 **构建和推送**：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-16-59@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
请注意 **Edgegap** 看不到您游戏的源代码。 \
他们只能看到一个完成的 Docker 容器。
{% endhint %}

您会看到一段时间的进度条：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-23-56@2x.png" alt=""><figcaption></figcaption></figure>

Unity 将创建一个 Linux 构建，然后创建一个 Docker 构建，然后将整个内容上传到 Edgegap。请注意，上传时间会根据您的互联网连接而有所不同。上传时进度条会停止，但您可以检查操作系统的带宽使用情况，以查看是否仍在上传中：

<figure><img src="../.gitbook/assets/2023-11-03 - 19-17-10@2x.png" alt=""><figcaption></figcaption></figure>

如果一切正常，你将看到一个日志消息：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-25-05@2x.png" alt=""><figcaption></figcaption></figure>

如果失败，将显示错误。以下是一些常见问题和解决方法：

- **缺少 Linux 构建支持**：在 Unity Hub 中安装它。确保为您在项目中使用的 Unity 版本安装。一般在安装了 Linux 构建支持后会正常工作。
- **增量构建失败**：删除位于 /Assets 文件夹旁边的 /Builds 文件夹中的先前 Unity Linux 构建，重新启动 Unity 再试一次。如果需要，删除 Library/ 文件夹。这是 Unity 有时会出现的 bug。
- **Docker 授权失败**：确保 Docker Desktop 正在运行，并确保您使用上述终端命令登录。还要确保镜像注册表包含 **容器注册表** 的 'Project' + "/" + "your-game"。

如果遇到其他问题，请在我们的 **#edgegap** Discord 频道中讨论。我们希望为任何可能的问题找到解决方案，并在这里解释！

## 部署服务器

现在我们已经将服务器构建上传到 Edgegap，我们需要 **部署**（即启动）它。

在插件中，点击 **创建新部署**：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-27-46@2x.png" alt=""><figcaption></figcaption></figure>

几秒钟后，您将在列表中看到正在运行的服务器：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-29-04@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
请注意，我们原始的端口是 **7777**，但在部署中显示为 **30358**。\
这是因为一个云服务器可能启动多个游戏服务器。\
但只有 **一个** 端口 **7777**，所以它使用 **端口映射** 为您找到一个空闲端口。
{% endhint %}

## 连接您的游戏客户端

最后，在 Unity 编辑器中按 **播放**（或启动您的客户端构建）。

在 NetworkManager HUD 中输入部署的 **主机名** 和 **端口**：

<figure><img src="../.gitbook/assets/2023-12-07 - 14-30-36@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
输入主机名(fa\[...].edgegap.net)和端口号(30358)！
{% endhint %}

按下**Client**按钮以连接，现在您已经在线了！ [🚀](https://emojipedia.org/rocket)

<figure><img src="../.gitbook/assets/2023-12-07 - 14-34-37@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
重要的是要理解这里发生的魔法。\
现在不仅可以用**两次点击**启动游戏服务器。\
您甚至可以在**Edgegap**的网站上再次点击来启动**成千上万个服务器**！ 🤩
{% endhint %}

为了降低成本(如果您在支付)，您可以在插件中完成后按下**Stop Server**。

<figure><img src="../.gitbook/assets/2023-12-07 - 14-43-38@2x.png" alt=""><figcaption></figcaption></figure>

## Optional: 在运行时获取服务器状态

如果您需要从上述截图中获取服务器状态，您可以使用：

```csharp
Edgegap.Status status = Edgegap.EdgegapServerDataManager.GetServerStatus();
```

## 解决连接问题

如果您的服务器状态显示为**Ready**但似乎无法连接，请尝试以下操作：

- 在 Edgegap 网站上，转到 Deployments -> 选择您的 Deployment -> 选择**Container Logs**，检查日志文件，查看您的游戏服务器是否实际启动或是否存在问题。
  - 如果显示"exec user process caused: no such file or directory"：这可能是因为您将 ARM 构建推送到 Edgegap 的 x86 基础设施。我们已经更新了插件，以便从 ARM 正确交叉编译，因此通常不应再发生这种情况。
- 如果一切看起来正常但仍无法连接，请在 Mirror Discord 的**#edgegap**频道与 Edgegap 员工交谈。&#x20;

{% hint style="info" %}
非常感谢我们加拿大 🇨🇦 的**Edgegap**朋友为 Mirror 用户提供免费的 0.5 vCPU！\
虽然我们希望未来有更大的免费层，但请记住，他们是为基础设施付费的人。如果有能力，请为更多 vCPUs 支付给他们！
{% endhint %}
