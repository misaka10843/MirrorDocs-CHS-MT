# 多路传输(Multiplex Transport)

多路传输并不是一种传输方式，而是允许您将其他传输方式组合在一起，以便您的客户端可以通过其中任何一种连接到服务器。

多路传输的常见用例是服务器同时监听 WebGL 和移动端/桌面端客户端。您的 WebGL 客户端可以使用 [Websocket 传输(Websocket Transport)](websockets-transport/) 连接到服务器，而移动端或桌面端客户端可以通过 TCP 或 UDP 传输连接到同一服务器。您可以在多路传输中配置任意数量的传输方式。

要使用多路传输，请按照以下步骤进行操作：

1. 如果尚未这样做，请向场景中添加一个带有 Network Manager 的游戏对象
2. 默认情况下，Mirror 将向您的 Network Manager 游戏对象添加 KCP 传输(KCP Transport)
3. 向游戏对象添加一个多路传输(Multiplex Transport)组件
4. 在 Network Manager 的传输字段中分配多路传输组件
5. 向游戏对象添加一个 Websocket 传输(Websocket Transport)组件
6. 将 KCP 传输组件添加到多路传输中作为第一个传输方式
7. 将 Websocket 传输组件添加到多路传输中作为第二个传输方式

请注意，KCP 和 Websocket 传输无法监听相同的端口。默认情况下，KCP 监听 7777 (UDP)，而 Websocket 传输监听 7778 (TCP)。

如果将游戏构建为 WebGL 游戏，则将跳过 KCP 传输，客户端将使用 Websocket 传输。如果将游戏构建为移动端或桌面应用程序，则将选择 KCP 传输。服务器将高兴地接受来自两者的连接。

以下是配置的样式：

<div align="left">

<img src="../../.gitbook/assets/image (103).png" alt="">

</div>

(Original: Multiplex Transport)
