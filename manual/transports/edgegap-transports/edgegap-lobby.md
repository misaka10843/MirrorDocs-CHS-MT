# Edgegap 大厅 (Edgegap Lobby)

`EdgegapLobbyKcpTransport` 使用 Edgegap 的 [大厅服务 (Lobby Service)](https://docs.edgegap.com/docs/lobby/functions) 来启动中继会话并为您管理大厅。它使用 [KCP](../kcp-transport.md) 来进行实际连接。

这绝对是使用中继的最简单方式，基本上不需要任何设置，如果您只需要点对点/玩家托管大厅而无需进行匹配。

大厅传输允许您创建/加入大厅并列出玩家可以选择的可用大厅。

我们提供了一个 [简单的大厅 UI 示例，让您可以快速上手](../../examples/edgegap-lobby.md)

### 设置 (Setup)

设置非常简单，您只需要部署一个大厅服务。您可以通过 [Edgegap API 手动进行部署](https://docs.edgegap.com/api/#tag/Lobbies/operation/lobby-create)，或者使用传输中内置的 UI。

<figure><img src="../../../.gitbook/assets/image (151).png" alt=""><figcaption><p>在 EdgegapLobbyKcpTransport 上的创建和部署大厅按钮</p></figcaption></figure>

点击“创建和部署大厅”按钮后，将会打开一个窗口：

<figure><img src="../../../.gitbook/assets/image (154).png" alt=""><figcaption><p>创建和部署大厅窗口</p></figcaption></figure>

只需输入您的 Edgegap API 密钥（如果您没有，点击“我没有 API 密钥？”按钮将方便地将您引导到 Edgegap 仪表板页面，您可以在那里创建一个），然后输入您选择的唯一大厅名称，然后点击创建。它会花费一小段时间，因为它会等待服务完全部署，然后传输上的大厅 URL 将自动填充。

完成这些步骤后，您就可以开始了。
