# IIS (IIS)

一旦您的 Windows Web 服务器运行起来并且可以通过 RDP 访问，您可能需要安装一些可选模块：

- URL 重写模块 - 在[这里](https://iis-umbraco-dr.azurewebsites.net/downloads/microsoft/url-rewrite)下载并安装。
- 应用程序请求路由 (ARR) - 在[这里](https://www.microsoft.com/en-us/download/details.aspx?id=47333)下载并安装。

从开始菜单中键入 IIS，您将能够选择 Internet 信息服务 (IIS) 管理器。

在 IIS 管理器中，选择左侧面板中“开始页面”下方的服务器，并在主窗口底部的“管理”部分打开“配置编辑器”。

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (32).png" alt=""><figcaption><p>IIS 管理器 - 配置编辑器在管理部分</p></figcaption></figure>

</div>

将“部分选择器”更改为 `system.webServer/proxy`，如此图所示，并将“启用”设置为 True，然后单击“应用”（右上角）。

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

</div>

### 初始站点绑定

现在在 IIS 管理器的左侧面板中，展开服务器和站点，选择“默认网站”。

- 在右侧面板中，单击“绑定”。
- 在“绑定”窗口中，双击端口 80 的单个条目。
- 在“主机名”字段中，键入您希望客户端连接的完全限定域名 (FQDN)，然后单击“确定”，然后关闭“站点绑定”窗口。
- 这将是您已注册并设置 DNS 指向此 Web 服务器的域名。

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (21).png" alt=""><figcaption><p>编辑站点绑定 - 输入您的 FQDN 并单击确定</p></figcaption></figure>

</div>

完成后应如下所示：

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (31).png" alt=""><figcaption><p>带主机名的站点绑定</p></figcaption></figure>

```markdown
### 应用程序池回收 (App Pool Recycling)

由于所有客户端都将通过 IIS 连接，而 IIS 使用一个“工作进程”默认在一定时间间隔内自行回收，我们需要禁用回收，因为这会导致所有客户端断开连接。

* 仍然选择默认网站，在右侧面板中点击“基本设置”，并记下显示在窗口中的应用程序池名称，然后关闭该窗口。
* 在左侧面板中，点击“应用程序池”，在主面板中选择在上面的“基本设置”中分配的应用程序池，然后在右侧面板中点击“回收”。
* 在“编辑应用程序池回收设置”中，确保没有选中任何选项（如下所示），然后点击“下一步”，然后点击“完成”。

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (12).png" alt=""><figcaption><p>编辑应用程序池回收设置</p></figcaption></figure>

</div>

### SSL 证书 (SSL Certificate)

{% hint style="warning" %}
注意：在进行此步骤之前，需要完成指向此服务器的 DNS 记录，并将其传播到全球 DNS 服务器。如果无法使用浏览器访问此站点，则无法使用 WinAcme 设置 SSL。
{% endhint %}

您需要为您的域名获取 SSL 证书。

* 从 [这里](https://www.win-acme.com/) 下载 WinAcme 并保存到自己的文件夹中，然后解压到同一文件夹中（这样以后更新时可以方便地覆盖为更新版本）。
* 运行 WACS.exe 并按照提示使用默认设置为 IIS 中的默认站点创建证书。在此步骤之后，您将为游戏客户端和代理更新绑定。
* WinAcme 创建一个定期更新 SSL 的计划任务，以防止证书过期。

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (61).png" alt=""><figcaption><p>WinAcme 控制台应用程序</p></figcaption></figure>

</div>

### 附加站点绑定 (Additional Site Bindings)

成功使用 WinAcme 安装 SSL 后，返回到 IIS 管理器，选择默认站点，再次点击“绑定”，就像之前一样操作。

您会看到 WinAcme 已经为端口 443 添加了一个绑定，使用了与端口 80 相同的主机名。
```

你可以双击端口443的绑定，查看SSL证书的设置情况。

点击“添加”以创建一个端口7777的额外绑定，如下所示，使用与端口443相同的SSL证书。这将用于客户端连接到稍后在本文档中设置的反向代理。如果您在同一服务器上运行多个游戏服务器实例，请为每个使用的端口添加一个绑定。确保防火墙已打开所有端口。

<div align="left">

<figure><img src="../../../../../.gitbook/assets/IIS Bindings.png" alt=""><figcaption><p>IIS绑定</p></figcaption></figure>

</div>

### 简单Web传输

确保简单Web传输设置如下：

* 启用SSL：**未选中**
* SSL协议：**Tls 12**
* 服务器端口：**27777**
* 客户端使用Wss：**已选中**
* 客户端端口选项：**指定端口**
* 自定义客户端端口：**7777**

<div align="left" data-full-width="false">

<figure><img src="../../../../../.gitbook/assets/image (153).png" alt=""><figcaption><p>带有反向代理设置的简单Web传输</p></figcaption></figure>

</div>

### 构建和部署

在Unity中，Network Manager中，将网络地址更改为与上述绑定中使用的名称相同，例如game.example.com。

* 在构建设置中选择专用服务器平台，构建并部署服务器到服务器上的一个文件夹。Mirror将在指定的端口（上图中的27777）上启动服务器。
* 在构建设置中更改平台为WebGL，构建客户端，并将其上传到您的服务器上的文件夹，该文件夹指向IIS中指向的默认网站。

### 用于IIS的web.config

在IIS管理器中，右键单击您之前配置的默认站点，然后选择“浏览”。这是您将部署WebGL构建文件的文件夹。

* 如果该文件夹中已经有一个`web.config`文件，请在记事本中打开它，否则创建一个空文件并打开它。
* 将下面的XML合并到可能已经存在的内容中，保存并关闭。
* 返回到IIS，选择服务器本身，然后在右侧面板中点击“重新启动”。 

### [Build and Deploy](#build-and-deploy) (构建和部署)

以下是 web.config 文件的假设内容：

- 你已经将服务器构建为监听端口 `27777`，并且 WebGL 客户端连接到 `7777`。
- 你已经使用 brotli（首选）或 gzip 压缩构建了 WebGL 客户端。
- 你已经按照上面所示设置了绑定。
- 如果你为更多服务器创建了绑定，你需要在下面的规则部分添加额外的反向代理规则，通过复制/粘贴并更改模式和操作中的端口。

```
<configuration>
    <system.webServer>
        <!--
        Compressed Unity builds without decompression fallback can't be properly hosted on a server which
        has static compression enabled because this might result in the build files being compressed twice.
        The following line disables static server compression.
        -->
        <urlCompression doStaticCompression="false" doDynamicCompression="false" />
 
        <!-- To host compressed Unity builds, the correct mimeType should be set for the compressed build files. -->
        <staticContent>
            <!--
            NOTE: IIS will throw an exception if a mimeType is specified multiple times for the same extension.
            To avoid possible conflicts with configurations that are already on the server, we remove the mimeType for
            the corresponding extension using the <remove> element, before adding mimeType using the <mimeMap> element.
 
            The following lines are required for builds compressed with gzip, which don't include decompression fallback.
            The correct MIME type here would be application/octet-stream, but due to Safari bug
            https://bugs.webkit.org/show_bug.cgi?id=247421, it's preferable to use MIME Type application/gzip instead.
            -->
            <remove fileExtension=".data.gz" />
            <mimeMap fileExtension=".data.gz" mimeType="application/gzip" />
            <remove fileExtension=".wasm.gz" />
            <mimeMap fileExtension=".wasm.gz" mimeType="application/wasm" />
            <remove fileExtension=".js.gz" />
            <mimeMap fileExtension=".js.gz" mimeType="application/javascript" />
            <remove fileExtension=".symbols.json.gz" />
            <mimeMap fileExtension=".symbols.json.gz" mimeType="application/octet-stream" />
 
            <!--
            The following lines are required for builds compressed with Brotli, which don't include decompression fallback.
            -->
            <remove fileExtension=".data.br" />
            <mimeMap fileExtension=".data.br" mimeType="application/octet-stream" />
            <remove fileExtension=".wasm.br" />
            <mimeMap fileExtension=".wasm.br" mimeType="application/wasm" />
            <remove fileExtension=".js.br" />
            <mimeMap fileExtension=".js.br" mimeType="application/javascript" />
            <remove fileExtension=".symbols.json.br" />
            <mimeMap fileExtension=".symbols.json.br" mimeType="application/octet-stream" />
        </staticContent>
 
        <!--
        NOTE: IIS will throw an exception if the following section is used without the "URL Rewrite" module installed.
        Download the "URL Rewrite" module from https://www.iis.net/downloads/microsoft/url-rewrite
        -->
        <rewrite>
            <rules>
                <clear />
                <rule name="Force HTTPS" enabled="true" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{HTTPS}" pattern="^OFF$" />
                    </conditions>
                    <action type="Redirect" url="https://{HTTP_HOST}{REQUEST_URI}" redirectType="Permanent" />
                </rule>
                <rule name="Allow 443" enabled="true" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{SERVER_PORT}" pattern="443" />
                    </conditions>
                    <action type="None" />
                </rule>
                <rule name="Reverse Proxy" enabled="true" stopProcessing="true">
                    <match url=".*" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{SERVER_PORT}" pattern="7777" />
                    </conditions>
                    <action type="Rewrite" url="http://localhost:27777" appendQueryString="false" logRewrittenUrl="true" />
                </rule>
            </rules>
        </rewrite>
 
        <outboundRules>
            <!--
            Hosting compressed Unity builds without decompression fallback relies on native browser decompression,
            therefore a proper "Content-Encoding" response header should be added for the compressed build files.
 
            NOTE: IIS will throw an exception if the same rule name is used multiple times.
            To avoid possible conflicts with configurations that are already on the server,
            you should remove the mimeType for the corresponding extension using the <remove> element,
            before adding mimeType using the <mimeMap> element.
            -->
 
            <!-- The following section is required for builds compressed with gzip, which don't include decompression fallback. -->
            <remove name="Append gzip Content-Encoding header" />
            <rule name="Append gzip Content-Encoding header">
                <match serverVariable="RESPONSE_Content-Encoding" pattern=".*" />
                <conditions>
                    <add input="{REQUEST_FILENAME}" pattern="\.gz$" />
                </conditions>
                <action type="Rewrite" value="gzip" />
            </rule>
            
            <!-- The following section is required for builds compressed with Brotli, which don't include decompression fallback. -->
            <remove name="Append brotli Content-Encoding header" />
            <rule name="Append brotli Content-Encoding header">
                <match serverVariable="RESPONSE_Content-Encoding" pattern=".*" />
                <conditions>
                    <add input="{REQUEST_FILENAME}" pattern="\.br$" />
                </conditions>
                <action type="Rewrite" value="br" />
            </rule>
        </outboundRules>
    </system.webServer>
</configuration>
```
