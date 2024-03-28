# iOS AppStore(iOS AppStore)

**iOS AppStore UDP(iOS AppStore UDP)**

Mirror的默认kcp传输使用UDP套接字。\
第一次将您的应用提交到App Store审核流程时，有些人的应用会被拒绝，因为苹果的VPN似乎无法处理UDP。

我们不清楚原因，但这里有一些人提出的解决方法：

* cooper: "使用像Telepathy这样的TCP传输提交您的应用。一旦获得批准，下一个更新再切换到KCP。据说这样可以解决问题。"
* Ninja: "我的应用一直被拒绝，直到我实际上要求手动干预，然后才通过了"
* 为苹果发布使用复用器，例如kcp作为主要传输方式，telepathy作为备用传输方式。

**iOS AppStore LAN Broadcasting(iOS AppStore局域网广播)**

按照Xcode的说明从苹果获取多播网络权限。

将其添加到应用程序配置文件中，然后将功能添加到应用程序本身，确保您已经在Xcode中正确添加了应用程序的授权。

这应该使网络发现功能正常工作，感谢overmatch-iman报告了有效步骤。
