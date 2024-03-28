# Linux (Linux)

在 Linux 上，您有多种选择可以作为反向代理的软件。常见选项可能包括 [Nginx（nginx）](nginx.md), [Caddy（caddy）](caddy.md), [Apache（apache）](apache.md), [HAProxy（HAProxy）](ha-proxy.md) 等。

我们将在这里详细介绍一些常见的选择，通常您可能只想坚持使用您熟悉或已经在使用的软件。

以下指南（除了 caddy）将假定您已经设置了 certbot 并获得了证书。它们也是针对 Debian 编写的，但在其他发行版上应该非常相似（如果不是完全相同）。

确保 Simple Web Transport 设置如下：

- Ssl Enabled: **未选中**
- Ssl Protocols: **Tls 12**
- Server Port: **27777**
- Client Use Wss: **选中**
- Client Port Option: **指定端口**
- Custom Client Port: **7777**

<div align="left">

<figure><img src="../../../../../.gitbook/assets/image (153).png" alt=""><figcaption><p>带有反向代理设置的 Simple Web Transport</p></figcaption></figure>

</div>
