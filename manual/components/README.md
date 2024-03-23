# 组件

这些核心组件包含在 Mirror 中：

* [网络动画器](network-animator.md)\
  网络动画器组件允许您同步网络对象的动画状态。它会同步来自动画控制器的状态和参数。
* [网络认证器](network-authenticators/)\
  网络认证器有助于将用户账户和凭据集成到您的应用程序中。
* [网络发现](network-discovery.md)\
  网络发现使用 LAN 上的 UDP 广播，使客户端能够找到正在运行的服务器并连接到它。
* [网络身份](network-identity.md)\
  网络身份组件是 Mirror 网络高级 API 的核心。它控制游戏对象在网络上的唯一身份，并利用该身份使网络系统意识到游戏对象的存在。它提供两种不同的配置选项，它们是互斥的，这意味着两个选项中的一个或者都不选中。
* [网络管理器](network-manager.md)\
  网络管理器是用于管理多人游戏的网络方面的组件。
* [网络管理器 HUD](network-manager-hud.md)\
  网络管理器 HUD 是一个快速启动工具，帮助您立即开始构建多人游戏，而无需首先为游戏创建/连接/加入构建用户界面。它允许您直接开始游戏编程，并且意味着您可以在开发进度表中稍后构建自己版本的这些控件。
*   [网络 Ping 显示](network-ping-display.md)

    网络 Ping 显示在 OnGUI 中显示客户端的 Ping 时间
*   [网络刚体](network-rigidbody.md)

    网络刚体同步刚体的速度和其他属性在网络中传输。
*   [网络插值刚体](network-lerp-rigidbody.md)

    网络插值刚体组件在网络中同步刚体的位置和速度。&#x20;
* [网络房间管理器](network-room-manager.md)\
  网络房间管理器是 Network Manager 的扩展组件，提供基本的房间功能。
* [网络房间玩家](network-room-player.md)\
  网络房间玩家是在房间场景中与上述网络房间管理器一起使用的玩家预制体上必需的组件。
* [网络起始位置](network-start-position.md)\
  网络起始位置在创建玩家对象时由网络管理器使用。网络起始位置的位置和旋转用于放置新创建的玩家对象。
* [网络统计信息](network-statistics.md)\
  通过 OnGUI 显示网络消息和每秒发送和接收的字节数。
* [网络变换](network-transform/)\
  网络变换组件同步游戏对象在网络中的移动和旋转。请注意，网络变换组件仅同步生成的网络游戏对象。

请粘贴您要翻译的 Markdown 内容。
