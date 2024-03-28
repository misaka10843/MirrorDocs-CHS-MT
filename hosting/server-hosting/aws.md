# AWS(Amazon Web Services)

在这一节中，我们将专注于使用 Windows 实例并从 Windows 计算机进行连接。

> **注意**: 在能够使用 Windows 服务器之前，AWS 需要添加付款方式/信用卡。

不要忘记阅读有关[免费套餐限制](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-free-tier.html)。在这个示例中，我们将使用 EC2 选项的免费套餐，这使我们有可能使用 Windows 实例（如果您已经添加了付款方式）。有关哪些服务提供免费套餐的更多信息，请参见[AWS 免费套餐](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank\&all-free-tier.sort-order=asc\&awsf.Free%20Tier%20Types=tier%2312monthsfree\&awsf.Free%20Tier%20Categories=categories%23compute)。

以下是我们将从头到尾执行的步骤：

1. 创建账户
2. 使用 EC2 管理控制台设置实例
3. 通过 RDP（远程桌面）配置服务器
4. 设置防火墙以允许连接
5. 测试连接

## 1. 创建账户（Account Creation）<a href="#1-account-creation" id="1-account-creation"></a>

转到[创建账户页面](https://portal.aws.amazon.com/billing/signup?nc2=h\_ct\&src=default\&redirect\_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation#/start)，使用您的详细信息和付款方式（例如信用卡）注册。添加付款方式是在 AWS 上运行 Windows 实例所必需的。

## 2. 使用 EC2 管理控制台设置实例（Setting up an instance with the EC2 Management Console）<a href="#2-setting-up-an-instance-with-the-ec2-management-console" id="2-setting-up-an-instance-with-the-ec2-management-console"></a>

设置完您的账户后，您应该已经登录。始终确保您在正确的区域，因此检查右上角是一个好习惯。现在将其更改为距离您最近的位置。之后，点击左上角的“Services”，然后点击“EC2”。这将带您到 EC2 实例仪表板，并准备启动您的实例！

### 请点击 **"启动实例"**。(Launch Instance)

![](<../../.gitbook/assets/image (37).png>)

创建 AWS 实例的 7 个步骤：

1. 选择 Amazon Machine Image (AMI)（Amazon 机器映像）
2. 选择实例类型（Instance Type）
3. 配置实例（Configure Instance）
4. 添加存储（Add Storage）
5. 添加标签（Add Tags）
6. 配置安全组（Configure Security Group）
7. 审查（Review）

### 步骤 1: 选择 Amazon Machine Image <a href="#step-1-choose-an-amazon-machine-image" id="step-1-choose-an-amazon-machine-image"></a>

向下滚动直到看到 Microsoft Windows Server 2019 Base（如果已过时，请选择最新的）。确保您选择的选项在图标下方有 "Free Tier Eligible"，然后点击 "选择"。

![](<../../.gitbook/assets/image (11).png>)

### 步骤 2: 选择实例类型 <a href="#step-2-choose-instance-type" id="step-2-choose-instance-type"></a>

确保选择具有 "free tier eligible" 的实例类型，在撰写本文时，这是 t2.micro 实例。点击 **"下一步: 配置实例详细信息"**。**不要**点击审查并启动。

![](<../../.gitbook/assets/image (118).png>)

### 步骤 3: 配置实例 <a href="#step-3-configure-instance" id="step-3-configure-instance"></a>

在此步骤中无需更改任何内容。保持默认设置。点击 **"下一步: 添加存储"**。

### 步骤 4: 添加存储 <a href="#step-4-add-storage" id="step-4-add-storage"></a>

无需更改任何内容。保持默认设置。30GB 的存储空间超出您的需求，但如果选择较低的数量会出现错误。请记住，如果需要返回，您可以随时点击 "上一步"，如果在最后点击启动，您不需要重复整个过程。

点击 **"下一步: 添加标签"** 继续。

### 步骤 5: 添加标签 <a href="#step-5-add-tags" id="step-5-add-tags"></a>

无需更改任何内容。保持默认设置。点击 **"下一步: 配置安全组"**。

### 步骤 6: 配置安全组 <a href="#step-6-configure-security-groups" id="step-6-configure-security-groups"></a>

> **重要步骤** 这使得可以从外部连接到您的实例：

- RDP (needed for this example)
- 对于通过端口7777连接到您的Mirror项目的客户端。
- SSH (如果需要)

创建一个新的安全组，您可以为其命名并添加描述。添加以下规则：

- RDP，源为“Anywhere”，描述可以随意，但请将其设置为远程桌面程序。
- 自定义TCP规则，端口7777，源为“Anywhere”，描述可以随意，但请将其设置为Mirror（如果KCP在您的NetworkManager上，请解锁UDP，而不是TCP）。
- SSH，源为“Anywhere”，描述可以随意，但请将其设置为SSH。

SSH并非绝对必要，但可以用于通过除RDP之外的其他方式远程连接。

您可以忽略关于源的警告，因为这只是一个测试环境。将来，您可能希望限制此内容，但您几乎永远不会预先知道客户端的连接IP。

点击**“下一步：审查并启动”**

![](<../../.gitbook/assets/image (44).png>)

### Step 7: Review（审查） <a href="#step-7-review" id="step-7-review">（原文："Step 7: Review"）</a>

就快完成了！确保一切正确，并且您正在使用免费层级，然后点击**“启动”**。

![](<../../.gitbook/assets/image (77).png>)

还有一件事：会弹出一个窗口要求您的密钥对。只需通过选择下拉菜单创建一个新的密钥对：**“创建新的密钥对”**，为其命名，然后点击**“下载密钥对”**。

将密钥文件（.PEM文件）保存在安全的地方（为了百分之百的确定性，请将其备份到其他地方）。

> **没有此密钥，您将无法访问已创建的实例**

![](<../../.gitbook/assets/image (98).png>)

现在您可以（终于）点击**“启动实例”**！

通过点击左上角的“服务”然后点击EC2返回到您的EC2仪表板。现在您会看到“运行中的实例：1”。**点击“运行中的实例”以继续**。

![](<../../.gitbook/assets/image (52).png>)

> 新实例可能需要大约5-10分钟来设置。如果没有任何变化，请在10分钟后刷新页面。

现在您已经完成了所有这些步骤，但是您想要进入专用服务器对吧？太好了！下一步将让您启动并运行！

## 3. 配置通过RDP（远程桌面）连接的服务器 <a href="#3-configuring-the-server-via-rdp-remote-desktop" id="3-configuring-the-server-via-rdp-remote-desktop"></a> (Configuring the server via RDP (Remote Desktop))

是时候获取RDP文件，这样您就可以开始连接了。我们需要一些东西：

1. 带有密钥对的RDP文件
2. 下载后配置RDP文件以允许从我们的C:驱动器或其他驱动器获取文件（这样您就可以轻松获取您的zip项目）
3. 在启动RDP文件后输入Windows管理员密码

完成这些步骤后，您应该能够继续使用相同的RDP文件。

### 带有密钥对的RDP文件 <a href="#rdp-file-with-the-key-pair-added-to-it" id="rdp-file-with-the-key-pair-added-to-it"></a> (RDP file with the key pair added to it)

![](<../../.gitbook/assets/image (79).png>)

> 在单击下载之前获取密码

> **复制密码以备后用**

![](<../../.gitbook/assets/image (102).png>)

现在单击**"下载远程桌面文件"**。将下载RDP文件。

![](<../../.gitbook/assets/image (46).png>)

### 配置RDP文件以便轻松访问文件 <a href="#configure-rdp-file-for-easy-file-access" id="configure-rdp-file-for-easy-file-access"></a> (Configure RDP file for easy file access)

转到您刚下载的RDP文件，右键单击它，然后单击"编辑"。

转到第三个选项卡"本地资源"，在底部单击"本地设备和资源"下的"更多"。在新窗口中选择您的C:驱动器或任何其他驱动器。这是您从中连接的自己的计算机。以便轻松交换文件。

![](<../../.gitbook/assets/image (43).png>)

太棒了！现在您可以运行RDP文件了！RDP文件将要求输入密码。如果您忘记了密码，可以右键单击实例，然后单击"获取Windows密码"来找回密码。您将被要求重新输入您的密钥对（.PEM）文件并解密消息。完成后，您将能够复制密码。

![](<../../.gitbook/assets/image (130).png>)

![](<../../.gitbook/assets/image (41).png>)

就是这样！现在您有了密码，远程桌面应该显示您登录到专用服务器！

### 设置防火墙以允许连接通过<a href="#setting-up-the-firewall-to-allow-connections-through" id="setting-up-the-firewall-to-allow-connections-through"></a>

进入 Windows 防火墙设置，进入高级防火墙设置，然后进入入站规则。**添加新规则**并选择端口类型。选择 TCP 并输入 7777 端口（如果在 Mirror 中使用其他端口，请使用其他端口）（如果在 NetworkManager 中启用了 KCP，请解锁 UDP，而不是 TCP）。继续点击下一步并保持默认设置。完成后关闭所有窗口。

![](<../../.gitbook/assets/image (123).png>)

太棒了！现在您已经设置好接受 **7777 端口**上的传入请求。

## 4. 测试连接<a href="#4-testing-the-connection" id="4-testing-the-connection"></a>

在您最终测试项目的服务器构建之前，您需要将其放在专用服务器上！

将您的（压缩的）服务器构建放在您添加的驱动器的根目录（C: 或其他位置），以便更快地找到它。

进入**“我的电脑”**，由于我们之前对 RDP 的更改，现在应该在“设备和驱动器”下看到您的本地驱动器。双击它，因为您将压缩的服务器构建放在该驱动器上，当加载完成时，您应该立即看到它。

![](<../../.gitbook/assets/image (76).png>)

现在在专用服务器的桌面上的一个新文件夹中解压项目并运行它。

想要测试一下在启动 Mirror 服务器后是否看到 7777 端口已打开吗？从 EC2 管理控制台获取您的实例的 IPv4 公共 IP，并在客户端上使用它连接到该 IP。

前往 [PortChecker](https://www.portcheckers.com/)，输入专用服务器 IP 地址并输入端口 7777。

> 注意：如果未运行游戏/项目，则端口将关闭。只有在服务器运行时才会打开端口。
