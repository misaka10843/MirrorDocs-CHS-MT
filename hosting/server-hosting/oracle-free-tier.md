---
description: 使用 Oracle 免费层托管的步骤说明。
---

# Oracle 免费层 (Oracle Free Tier)

## 创建和配置云主机

访问 [Oracle](https://oracle.com) 网站。点击 **查看账户**，然后点击 **免费注册 Cloud 层**。

![](../../.gitbook/assets/image_01.png)

点击 **免费开始**，填写表格并创建您的账户。使用您的 Oracle 账户登录，如果您的账户正在验证中，请等待几分钟以继续下一步。点击 **使用向导设置网络**。选择 **创建带有互联网连接的 VCN**，然后点击 **开始 VCN 向导**。

![](<../../.gitbook/assets/image_02 (1).png>)

填写 **VCN 名称** 并禁用选项 **在此 VCN 中使用 DNS 主机名**，点击 **下一步**，然后等待。

![](../../.gitbook/assets/image_03.png)

点击 **VCN 名称**，以创建规则。选择 **Public-Subnet-network**，然后点击 **网络的默认安全性列表**。

![](../../.gitbook/assets/image_04.png)

点击 **添加入站规则**，并为以下内容创建规则：

* 协议 TCP
  * 端口范围：80、443 和 7777
  * 选择无状态选项
* 协议 UDP
  * 端口范围：80、443 和 7777
  * 选择无状态选项

![](../../.gitbook/assets/image_05.png)

转到 **主页**，点击 **创建 VM 实例**。填写 **计算机实例名称**，然后点击 **编辑**，在 **镜像和形状** 组中。

![](../../.gitbook/assets/image_06.png)

点击 **更改镜像**，选择 **Canonical Ubuntu**，操作系统版本 **18.04**，然后点击 **选择镜像**。

![](../../.gitbook/assets/image_07.png)

在 **网络组** 中检查网络是否是之前步骤中创建的网络。

![](../../.gitbook/assets/image_08.png)

在 **添加 SSH 密钥** 中，点击 **保存私钥**。将其保存在安全的地方，这将是访问您的机器所必需的。

![](../../.gitbook/assets/image_09.png)

在引导卷中，您可以指定硬盘大小，或者您可以在一个机器上使用您的 100 GB。点击创建并等待。返回主页，点击 **仪表板** > **计算** > **实例**，选择您的实例并复制 **IP 地址**。


![](../../.gitbook/assets/image_10.png)

## 访问您的机器（Access your machine）

打开 PowerShell 编辑器并输入以下命令：

```csharp
ssh -i [ssh_key_place] ubuntu@[machine_ip]
```

示例：

```csharp
ssh -i c:\users\user\desktop\ssh-key-2021-09-15.key ubuntu@0.0.0.0
```

> 如果连接到云机器时出现错误，请右键单击 ssh-key，选择属性 > 安全 > 高级 > 移除继承，并删除所有其他用户，仅保留您的 Windows 用户。

连接后，运行以下命令，将防火墙规则添加到 Ubuntu：

```csharp
sudo apt install firewalld  
sudo firewall-cmd --zone=public --add-port=7777/tcp –permanent  
sudo firewall-cmd --zone=public --add-port=7777/udp –permanent  
sudo firewall-cmd –reload
```

下载 FileZilla，并按照以下步骤连接到云机器，使用您的 ssh-key：

[https://filezillapro.com/docs/v3/basic-usage-instructions/ssh-private-keys-for-sftp/](https://filezillapro.com/docs/v3/basic-usage-instructions/ssh-private-keys-for-sftp/)

## 创建构建（Create Build）

在 Unity 中，访问 **文件** > **构建设置** > **选择 Linux** > **标记服务器构建**。等待构建完成，然后使用 FileZilla 上传您的文件。

![](../../.gitbook/assets/image_11.png)

本教程使用了一个服务器文件夹，但这是可选的，使用以下命令：

```csharp
ssh -i c:\users\user\desktop\ssh-key-2021-09-15.key ubuntu@0.0.0.0
cd server
chmod +x ./[your_project_name].x86_64  
./[your_project_name].x86_64
```

您的服务器正在运行


