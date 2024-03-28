---
description: Mischa的不寻常但方便的远程桌面托管方法。
---

# 使用远程桌面进行托管

请参考我们的"[实用托管指南](pragmatic-hosting-guide.md)"，了解一种现代的最佳实践托管方法。\
本指南介绍了我的不寻常的远程桌面托管方法，不涉及SSH、SCP、终端等。

{% hint style="info" %}
这绝对不是最佳实践，也不节约资源。\
但非常易于使用 :smile:。
{% endhint %}

这是最终结果的预览，这是一个在Google Cloud VM实例上运行的Debian桌面环境，我通过Google远程桌面从浏览器登录：

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption><p>一个运行着桌面环境的Google Cloud VM实例。</p></figcaption></figure>

这使得我可以像在本地机器上一样工作流程。我可以轻松浏览文件系统，检查资源使用情况，查看长时间运行的任务，使用Firefox下载文件等，而无需担心终端命令。

对我来说，易用性和便利性比几百兆字节的存储更重要。\
但再次强调，这不是最佳实践。

### 安装说明

{% hint style="info" %}
本指南专门使用**Google Cloud**、**Debian**、**XFCE**和**Google远程桌面**，\
因为这种组合是最简单和最可靠的。欢迎尝试其他方法。
{% endhint %}

这种方法基于Google Cloud官方的[使用Google远程桌面指南](https://cloud.google.com/solutions/chrome-desktop-remote-on-compute-engine)在他们的VM实例上。然而，我在使用过程中发现了一些建议：

* 使用至少**15 GB**的磁盘空间。
* 使用**Debian**。Ubuntu不适用于此方法。
* 预计会出现一些安装**错误**，这就是为什么Google在其中有--fix-broken命令。
* 使用**XFCE**作为桌面环境。Cinnamon不适用于此。
* 最好也安装**Firefox**，如他们的指南中所述。

按照指南操作后，您应该能够使用[Google远程桌面](https://remotedesktop.google.com/access/)登录。

### 额外: 文件传输(Extra: File Transfers)

Google 远程桌面的界面具有文件传输功能，但使用起来并不是很方便。

我发现使用任何流行的云文件同步工具更容易。\
[Mega](https://mega.nz/) 在这方面效果很好，因为它有官方的 Linux 支持以及端到端加密。\
\
首先在本地计算机上使用官方下载安装它，并创建一个带有测试文件的文件夹。\
\
然后在云服务器上安装它，按照以下步骤进行：

```
  hostnamectl   // to see your debian version
  // next: use firefox to download the Mega client for Debian 10
  sudo apt install /home/USERNAME/Downloads/FILENAME.deb
  open via applications->system->megasync
```

### 摘要(Summary)

我们学会了如何在云服务器上安装桌面环境，如何通过 Google 远程桌面登录，并如何向其传输文件。

希望这对一些不太喜欢终端的人们更加方便。\
我们使用这种方法来管理我们的 Discord 机器人 (Alan)，以及其他一些工具。\
您也可以轻松地将其用于一些游戏服务器。\
\
但请记住，我们的 [务实托管指南](pragmatic-hosting-guide.md) 更适合严肃的游戏。
