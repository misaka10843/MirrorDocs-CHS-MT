# 指南(Guides)

## 网络概念概述(Networking Concepts Overview)

### 高级脚本 API <a href="#high-level-scripting-api" id="high-level-scripting-api"></a>(High Level Scripting API)

Mirror是一个“高级”网络库。使用它意味着您可以访问覆盖大多数多用户游戏常见需求的命令，而无需担心“底层”实现细节。Mirror允许您：

* 使用“网络管理器”控制游戏的网络状态。
* 运行“客户端托管”游戏，其中主机也是玩家客户端。
* 使用通用序列化器对数据进行序列化。
* 发送和接收网络消息。
* 从客户端向服务器发送网络命令。
* 从服务器向客户端进行远程过程调用（RPC）。
* 从服务器向客户端发送网络事件。

### 低级传输 API(Low Level Transport API)

Mirror需要一个低级[传输(Transport)](../transports/)来在字节\[]级别上连接/断开连接/发送/接收消息。

### 引擎和编辑器集成(Engine and Editor integration) <a href="#engine-and-editor-integration" id="engine-and-editor-integration"></a>

Mirror的网络功能已集成到引擎和编辑器中，使您可以使用组件和可视辅助工具来构建多人游戏。它提供：

* 用于网络对象的NetworkIdentity组件。
* 用于网络脚本的NetworkBehaviour。
* 可配置的对象变换自动同步。
* 脚本变量的自动同步。
* 支持将网络对象放置在Unity场景中。
* 网络组件
