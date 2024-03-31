# Google Cloud (Google 云)

## Google Cloud 服务 (Google 云服务) <a href="#google-cloud-services" id="google-cloud-services"></a>

本指南将解释如何在 [Google Cloud](https://cloud.google.com/) 上设置运行 Linux 服务器。

Google 云为新用户提供有限时间内的 $300 免费信用额度，并且还有“始终免费产品”。请查看 [概览](https://cloud.google.com/free) 和 [文档](https://cloud.google.com/free/docs/gcp-free-tier) 获取更多信息。

### 开始之前 <a href="#before-you-begin" id="before-you-begin"></a>

1. 在 Cloud 控制台的项目选择器页面上，选择或创建一个 Cloud 项目。

> 注意：如果您不打算保留此过程中创建的资源，请创建一个项目，而不是选择现有项目。完成这些步骤后，您可以删除该项目，从而删除与项目关联的所有资源。

[**转到项目选择器页面**](https://console.cloud.google.com/projectselector2/home/dashboard)

1. 您可能需要为您的 Google Cloud 项目启用计费。如果您有免费信用额度（请参见上文），那么这些将首先被使用。[了解如何确认为您的项目启用了计费。](https://cloud.google.com/billing/docs/how-to/modify-project)

### 设置新服务器 <a href="#setting-up-a-new-server" id="setting-up-a-new-server"></a>

首先创建并配置一个新的 Compute Engine 实例。

1. 在 Cloud 控制台中，转到 VM 实例页面：

[**转到 VM 实例页面**](https://console.cloud.google.com/compute/instances)

如果这是您的第一个实例，您将看到一个对话框提示您创建一个新的虚拟机

![创建新](../../.gitbook/assets/01-vm-instances.jpg)

1. 点击创建以开始。
2. 在创建实例页面上，按照以下配置您的实例：
   * 命名您的实例。本教程中始终使用实例名称 `mirror-instance-demo`。
   * 选择您希望实例托管的区域和区域。本教程中始终使用区域 `us-central1（爱荷华州）` 和区域 `us-central1-a`。
   * 机器配置取决于您的游戏大小。对于一个简单的示例，我们可以使用一个小型服务器。
     * 选择 `N1` 系列
     * 选择 `f1-micro` 作为机器类型
   * 在启动磁盘部分，点击更改。将弹出启动磁盘对话框。
   * 将磁盘类型更改为 SSD 持久磁盘。

![使用名称和区域创建实例](../../.gitbook/assets/02-create-instance-name-region.jpg)

1. 对于本教程，引导磁盘可以保留为默认的`10gb标准持久磁盘`，使用`Debian GNU/Linux 9 (stretch)`。

您可以根据自己的需求进行更改。您可以选择使用固态硬盘或增加其大小，但这将增加每月的成本。

![03引导磁盘](../../.gitbook/assets/03-boot-disk.jpg)

> 注意：月度估算应显示在`Name`旁边的右上角

1. 要配置网络设置，首先查看`管理、安全、磁盘、网络、独占租户`部分，然后单击`Networking`选项卡。

![展开网络设置](../../.gitbook/assets/04-expand-network-settings.jpg)

1. 设置一个网络标记，这将允许您稍后设置防火墙规则。

本教程使用`mirror-demo`作为标记

![设置网络标记](../../.gitbook/assets/05-network-tag.jpg)

1.  为您的实例设置静态 IP 地址

    a) 在**网络接口**下单击`default`，将会弹出一个配置菜单

    b) 选择**外部 IP**下拉菜单，选择`Create IP address`

    c) 输入一个名称用于此 IP 并按**Reserve**。本教程中我们将使用`mirror-demo-ip`

![保留 IP 地址](../../.gitbook/assets/06-ip-address.jpg)

1. 创建您的实例。

单击页面底部的**Create**按钮完成设置并创建您的实例

### 配置防火墙 <a href="#configure-firewall" id="configure-firewall"></a>

这将允许其他人使用其 IP 和端口连接到您的服务器

1. 在 Cloud 控制台中，转到防火墙页面。

[**转到防火墙页面**](https://console.cloud.google.com/networking/firewalls/list)

1. 单击创建防火墙规则。
2. 在创建防火墙规则页面上，填写以下表单：
   * 名称：`mirror-demo-rule`
   * 目标标记：`mirror-demo`
   * 源过滤器：IP 范围
   * 源 IP 范围：0.0.0.0/0
   * 协议或端口：选择 tcp，并在提供的字段中输入端口 7777。

> 注意：tcp 和端口 7777 是 telepathy 的默认设置，如果您使用不同的传输方式，则需要了解其使用的设置。&#x20;
>
> 如果 KCP 在您的 NetworkManager 上，解除 UDP 的阻止，而不是 TCP。

![创建网络规则](../../.gitbook/assets/07-create-network-rule.jpg)

# 连接到您的新实例（Connect to your new instance）

一旦您的实例创建完成，您可以点击 **SSH** 按钮在您的网络浏览器中连接到您的实例

![连接到实例](../../.gitbook/assets/08-connect-to-instance.jpg)

# 设置 Linux 实例（Setting up the linux instance）

当您首次连接时，您需要设置一些内容

1. 首先更新软件包列表

```bash
sudo apt-get update
```

2. 安装 `screen` 和 `unzip`

```bash
sudo apt-get install -y screen unzip
```

`screen` 允许您拥有多个终端，可以在游戏运行时进行其他操作

`unzip` 可以用于在将其上传到实例后从 ZIP 文件中提取文件

![更新和安装软件包](../../.gitbook/assets/09-update-and-install.jpg)

# 构建您的游戏（Build your game）

使用目标平台 `Linux` 并勾选 **Server Build** 来构建您的游戏

![服务器构建](../../.gitbook/assets/10-server-build.jpg)

# 上传并解压您的游戏（Upload and extract your game）

一旦构建完成您的游戏，请将文件夹压缩，以便您可以将其上传到服务器。

在 SSH 连接窗口中，点击设置齿轮图标并选择 **Upload File**，然后找到并选择您的压缩构建文件夹。

![上传文件](../../.gitbook/assets/12-uploading.jpg)

这将会将游戏文件夹上传到主目录。

如果您需要进入主目录，您可以使用：

```
cd ~
```

> 注意：目标路径将会是 `/home/username`，其中 `username` 应该是您登录的云账户。

![上传中](../../.gitbook/assets/11-upload-a-file.jpg)

一旦您的文件上传完成，您可以解压文件

```
unzip ./mirror-demo.zip
```

### 运行服务器（#run-server）

1. 进入游戏文件夹

```csharp
cd mirror-demo
```

2. 将游戏标记为可执行文件

```csharp
chmod +x ./mirror-demo.x86_64 
```

现在您应该可以运行游戏服务器了！

以下内容是可选的，但很可能会很有用

1. 在一个分离的屏幕中启动服务器

```csharp
screen -d -m -S mirrorServer ./mirror-demo.x86_64 -logfile ~/server.log
```

上述命令的各部分：

* `screen -d -m` 将以分离模式创建新的终端
* `-S mirrorServer` 屏幕的名称
* `./mirror-demo.x86_64` 您的游戏服务器文件
* `-logfile ~/server.log` Unity 的日志文件位置参数，这将在主目录中创建一个日志文件

2. 创建启动文件，以便每次都可以以相同的方式启动服务器

    a) 创建 `start.sh`

    ```csharp
    echo "screen -d -m -S mirrorServer ./mirror-demo.x86_64 -logfile ~/server.log" > ./start.sh
    ```

    b) 标记为可执行

    ```csharp
    chmod +x ./start.sh 
    ```

3. 运行您的服务器

```csharp
./start.sh
```

#### 使用 Screen（#using-screen）

以下是一些使用 `screen` 的有用命令

* 列出活动屏幕

```csharp
screen -ls
```

* 连接到现有屏幕，
  * 当调用 `screen -ls` 时会显示进程 ID
  * 创建屏幕时给出的屏幕名称，本教程中为 `mirrorServer`

```csharp
screen -r <process-id | screen name>
```

* 从屏幕中分离，按 `CTRL A + D`
* 停止游戏

一旦连接到屏幕，您可以按 `CTRL + C` 来停止进程，这将停止您的游戏并关闭屏幕

#### 查看日志文件（#view-log-files）

您可以在启动游戏服务器时使用 `-logfile ~/path/to/log.log` 参数来指定 Unity 的日志文件位置。这将导致日志被写入文件而不是终端。这意味着最近的日志不会显示在 `screen -r` 中。

* 您可以使用 `tail` 命令查看日志文件的末尾

```csharp
tail -n 50 ./server.log 
```

* 您可以使用 `-n` 参数设置要显示的行数
* 您可以使用 `-f` 参数跟踪文件流的末尾，实时显示写入文件的任何新日志。您需要按 `CTRL + C` 退出跟踪模式

### 教程结束后的清理 <a href="#clean-up-after-tutorial" id="clean-up-after-tutorial"></a>

如果您使用了现有项目，则需要删除以下内容：

- VM 实例
- 防火墙规则

如果您创建了新项目，您应该可以直接删除该项目。

[**转到管理资源页面**](https://console.cloud.google.com/iam-admin/projects)
