# 反向代理(Reverse Proxy)

<div align="left">

<figure><img src="../../../../.gitbook/assets/image (56).png" alt=""><figcaption><p>使用反向代理的 WebGL - 500 CCU</p></figcaption></figure>

</div>

使用反向代理比在 Unity 中作为游戏服务器进程进行 SSL 加密性能更好：

* Unity 是单线程的，而反向代理是一个独立的进程，通常被设计为企业级性能且是多线程的。
* 加密计算成本高昂，最好不要让 Unity 承担这个工作负担。
* Unity / mono 可能不会及时更新到最新的 TLS 版本，也可能无法读取最新的证书。

本节包含了针对 [Linux](linux/) 和 [Windows](windows/) 的说明。
