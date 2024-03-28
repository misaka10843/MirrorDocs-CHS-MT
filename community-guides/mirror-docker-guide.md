# Mirror Docker 指南（Guide）

要使用 Docker 与 Unity，您可以尝试这个指南：

{% embed url="https://docs.edgegap.com/docs/tools-and-integrations/unity-server-in-docker" %}

此外，我们的朋友 Andrew Allbright 分享了一个在 Docker + Docker Compose 上运行 Mirror 的示例：

> Mirror 服务器构建为专用的 Linux 服务器构建，并在 Ubuntu 服务器上运行。WebGL 资产由一个 nginx 服务器提供。另一个 nginx 容器充当反向代理，以便接受 websocket 连接。

{% embed url="https://github.com/aallbrig/unity-mirror-networking-containerized" %}
