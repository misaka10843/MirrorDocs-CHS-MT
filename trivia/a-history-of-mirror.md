---
description: Mirror存在的快速概述以及我们是如何到达这里的 - 由 vis2k
---

# Mirror简史 (A Brief History of Mirror)

我们的社区已经发展壮大。大多数人在这一切开始时并不在场，所以这里是UNET/Mirror的简史。

如果您是Mirror的新手，希望这篇文章能带给您一些乐趣。对于那些一直在这里的人来说，也许会带来一些怀旧的截图，让您回忆起过去。

{% hint style="info" %}
随着Mirror随着时间的发展，我们将不断更新这篇文章。
{% endhint %}

## UNET

![](<../.gitbook/assets/image (49).png>)

回到2015年夏天，Unity发布了**UNET**的公共测试版。Unity关注网络功能，让我们专注于游戏开发的想法听起来非常诱人。UNET带来了一些有趣的功能：

* **服务器和客户端在一个项目中**。大部分代码是共享的。有些标记为\[Server]或\[Client]。
  * 这样做可以极大提高生产力，因为地形、模型、资源和代码在服务器和客户端之间都是共享的。
* **\[SyncVars]** 用于自动序列化选定的变量。
  * 当从手动构建的序列化/反序列化函数转换时，这是非常重要的。只需在Player.level前面添加一个\[SyncVar]就简单多了。
* **\[Commands/Rpcs]** - 使用\[Command]标记包装一个函数，自动在服务器上调用它是另一个巨大的生产力提升。
  * 相比手动发送消息、反序列化所有参数并手动调用函数。

## 热度

回到2015年，几乎每个月都有关于UNET的讨论和视频，比如[Democratizing Multiplayer Development](https://www.youtube.com/watch?v=gZbbYXzyXKk)。每个人都很兴奋，UNET团队也在努力工作。

![Sean Riley在Unite 2014展示UNET](../.gitbook/assets/2021-04-02_18-35-53@2x.png)

![2015年早期的UNET讨论之一](../.gitbook/assets/2021-04-02_12-36-24@2x.png)

![Alexey Abramychev关于LLAPI的讲话](../.gitbook/assets/2021-04-02_18-45-38@2x.png)

那时候，主要是：

* **Alexey Abramychev**：友好的俄罗斯人，负责低级传输（LLAPI）
* **Sean Riley**：曾在魔兽世界工作，现在开发高级API（HLAPI/UNET）
* **Erik Juhl**：UNET团队负责人，他说MMO是他的激情

UNET团队在他们的**路线图**上有很大的计划：

- 云服务，如中继/匹配
- 从Unity编辑器直接进行一键式游戏服务器托管
- 将支持MMO规模网络的_模拟服务器_
- UNET的“第3阶段”也就是自动化服务器实例。标记一个区域，它将作为一个实例运行。

大多数多人游戏永远不会问世，因为完成所有那些代码极其困难。有了UNET，第一次似乎真的是可行的。生产力提升了一个数量级。

# Getting Started(开始)

UNET在2015年仍处于测试版。没有太多文档，但只有几个演示。其中之一是一个带有几个球在网络上移动的移动演示。

在UNET之前，我已经在进行我的MMO项目。移动球听起来与移动玩家相似，所以我尝试了UNET。

![UNET的移动演示，带有一些早期MMO功能](../.gitbook/assets/2015-09-30\_unet\_noicons.png)

通过SyncLists和所有的\[SyncVar]/\[Rpc]便利，网络化的NavMeshAgent移动、技能、装备和库存很容易添加。

在测试版期间，UNET并不是非常稳定，但生产力的提升是巨大的，看起来是未来多人游戏开发的明显路径。

## Meet Damon(遇见Damon)

大约在九月份，我还开始与Damon Slye交谈，他是1990年制作的[Red Baron](https://store.steampowered.com/app/263940/Red\_Baron\_Pack/)的人，由Sierra发行。

![Red Baron](<../.gitbook/assets/image (85).png>)

Damon和他的新团队Mad Otter Games实际上制作了他们自己的独立MMO，就像我计划做的那样。他们没有使用Unity，但考虑将其用于下一个项目。我们讨论了很多关于MMO技术、经验教训，他们甚至为我的未命名的MMO项目许可了我几个模型。

随着UNET团队不知疲倦地致力于网络工作，MadOtter团队作为活生生的证明，对我来说，独立MMO的想法似乎实际上几乎是可行的。

## What if...(如果...)

![2015年11月的MMO项目，带有许可的模型。](../.gitbook/assets/2021-04-02\_13-00-01@2x.png)

2015年，据我所知，当时没有任何类型的大型多人在线游戏（MMO）工具包。在我逆向工程的日子里，我记得完美世界娱乐公司授权他们的 MMO 引擎售价为 10 万至 30 万美元。可以说这超出了我的预算。

由于我本来就在开发自己的 MMO 代码，我想干脆将其放在 Asset Store 上，看看会发生什么。也许每月销售额为 50 美元，我就可以每周吃一次披萨。

[uMMORPG](https://assetstore.unity.com/packages/templates/systems/ummorpg-remastered-159401) 于 2015 年 12 月 23 日发布在 Asset Store 上。就在圣诞节前一天。

![uMMORPG 的早期版本](<../.gitbook/assets/2017-01-11\_10-42-44 (1).png>)

事实证明，很多人有着相同的梦想，但没有足够的资金来授权 AAA 引擎。

uMMORPG 很快成为商店中前 10 名的资产之一。

昨天，MMO 开发是我的爱好，是我业余时间工作的梦想。但一夜之间，由于 Unity 引擎、UNET 和一个巨大的赌注，它变成了全职工作。

## 在 UNET 我们信任

大约在那个时候，**Paul** 和我相遇，他在他的 Cubica 游戏中使用了 uMMORPG。

只有一个问题：UNET。我们把所有的信任都放在一个网络黑匣子和几位专业的网络工程师身上。

然而，即使提交了 30-50 个 bug 报告，恳求 UNET 团队的每个人 1-2 年，仍然没有任何进展。没有一个 bug 被修复。没有改进。没有答复。

我们的用户/客户每天都会遇到新的 UNET bug。糟糕的评价。随机崩溃。我们的梦想建立在一个抛弃我们的黑匣子上，而我们却无能为力。

最糟糕的是，UNET 无法扩展。30-70 CCU 和几十只怪物就是它所能承受的极限。

![UNET 早期社区测试，有 57 个连接和许多 bug。](<../.gitbook/assets/57 (1).png>)

有一次，我使用反射来修复 UNET 中的序列化 bug。当时，这是 Unity 问题跟踪器中评分最高的网络 bug。我在论坛上详细介绍了 bug 的原因以及如何修复它。我联系了 Alexey、Erik、Larus、Asset Store 团队和 QA。 

**（In 2015, there wasn't any type of MMO toolkit available as far as I could tell. From my reverse engineering days I remembered Perfect World Entertainment licensing their MMO engine for $100,000 - $300,000. Which needless to say was out of my budget.）**


它被设计成了关闭。两次。

!["By Design" - the UNET Bug that started Mirror](../.gitbook/assets/2021-04-02\_18-39-23@2x.png)

我们的梦想建立在UNET上，而在我的情况下，我的生计也是如此。我需要UNET来支付房租和吃饭。然而，我们完全信任的人却抛弃了我们。

- **Sean Riley** 是UNET的主要推动力。事实证明，他在测试版发布后离开了团队。
- **Alexey** 尽其所能填补空缺。有一段时间，他甚至在周日上论坛，甚至在度假期间回复了我的邮件。然而，某个时候，他也不再在Unity工作了。
- **Erik Juhl**（UNET团队负责人）后来加入了一家Web开发公司。

更糟糕的是，当每个人悄悄离开这艘沉船时，Unity却没有任何消息。死一般的寂静。我们花了几年的时间才了解到，UNET核心团队甚至不再为Unity工作。

有一次，我们甚至给Joachim Ante写了一封公开信。绝望的时刻。

## HLAPI Pro

令人惊讶的是，Unity中的某人**开源了HLAPI** - UNET的高级部分。

**LLAPI** - 低级传输 - 一直保持封闭源代码。

对于HLAPI，人们必须编译两个C# DLL，手动覆盖它们在Unity安装中，然后祈祷它与特定的Unity版本兼容。

代码没有注释，过度设计，充满错误和魔法。

但我们有源代码。终于，隧道尽头有了一丝曙光。

所以我发了这篇关于“**HLAPI Pro - UNET的可替代方案**”的论坛帖子。完全兼容，只是修复了一些以前必须使用反射来解决的bug。这个想法是为人们提供一个100%无风险的替代方案。使用HLAPI Pro来修复您的bug。随时返回原始的DLL。

据我记得，Paul实际上最初与TwoTen一起在MLAPI上工作。但他说服我**MIT**许可证HLAPI Pro，于是Mirror诞生了。

{% hint style="info" %}
**趣闻**: 多年来，我们从未真正弄清楚房间里的大象。Unity是如何实现这个复杂的UNET技术的，但后来却无法修复它？编织者是真正的外星技术，对于我们来说，制造这个东西的人仍然不明显。\
\
在研究时，有一次我们发现了**NetworkView**的代码。该组件实际上是Unity传统的Raknet网络的一部分。据我们所知，UNET是基于他们的传统系统。
{% endhint %}

## 遇见 Mirror（镜像）

在接下来的几年里，我们修复了数百个 UNET（Unity 网络）的 bug 并清理了代码。

然而，没有人相信 UNET。据说，设计毫无意义，永远无法扩展，并且永远无法适用于任何真正的游戏。唯一相信 Mirror 的人是那些已经在他们的项目中使用 UNET 并迫切需要 bug 修复的人。

![早期 Mirror 的数百只怪物压力测试](../.gitbook/assets/2017-08-13_stresstest.png)

有一次，我们向 Unity 请求捐赠，因为我们免费支持了他们整个 UNET 社区。他们告诉我们 UNET 没有未来，再投资这项技术毫无意义。

我们的网络英雄们抛弃了我们，并告诉我们我们的工作毫无意义。

## 打破枷锁

逐步地，我们打破了仍然将我们与 UNET 联系在一起的所有枷锁。

- 过了一段时间，有人开源了 **Weaver**，这样我们也可以修复它。
- 我们用 Telepathy 替换了 **LLAPI** 的黑盒子。我们因为使用 TCP 而受到了严厉批评。但至少，黑盒子终于是开放的，我们可以一次性修复 bug。多年来，我们还添加了许多其他传输方式，比如 KCP。
- Paul 找到了如何将 **HLAPI** 作为源代码插入，而无需替换 DLL。
- **Websockets** 以前在 LLAPI 中是封闭的，现在也终于是开放的。
- UNET **社区** 大部分也转向了 Mirror。

直到今天，我们在 Mirror 上的大部分工作都是关于打破枷锁和修复所有 bug。

以下是我们多年来收集的一些截图。享受怀旧之情。

![UNET Weaver 项目 / DLL](../.gitbook/assets/2017-08-05_patreon_header.png)

![Mirror 的第一个示例](../.gitbook/assets/2018-09-25_movedemo.png)

![HLAPI 的第一个社区测试](<../.gitbook/assets/Screenshot at Jul 28 13-09-17.png>)

![Geno Online，jagatai 早期使用 Mirror 尝试的 MMO](../.gitbook/assets/2017-09-08_jagatai_stream.png)

![uMMORPG 500 CCU 测试。我们开始时，UNET 处理约 70 CCU。](../.gitbook/assets/2021-04-02_17-38-22@2x.png)

![Mirror 中的 20,000 只怪物。当时 UNET 处理 12 只怪物。](<../.gitbook/assets/2020-10-12_20-45-17 - x branch 20k monsters 19 FPS.png>)

## 社区 (Community)

自UNET测试版发布以来已经过去了6年。Mirror每年的下载量已经增长到了10万次，拥有一个庞大的Discord社区，拥有1万名用户，新增了像我们著名的网络传奇人物MrGadget、Coburn、James、katori、Lymdum、uwee等许多新团队成员。

![](../.gitbook/assets/2021-04-02\_17-52-33@2x.png)

有几款[热门游戏](https://mirror-networking.com/showcase/)是使用Mirror制作的，当然还有很多[梗](https://www.youtube.com/watch?v=jNTFFYhNhiI)。

![Population: ONE - 使用Mirror制作](<../.gitbook/assets/image (69).png>)

## 接下来 (What's Next)

我们花了很多时间打破束缚和修复bug。数以百计。

但自那时以来，我们也学到了很多关于网络的知识。

在未来，我们的[路线图](https://trello.com/b/fgAE7Tud)不再只是关于修复bug，而是关于核心网络技术。

虽然Mirror目前很稳定，但还有很长的路要走，原始使命从未改变。

我们只是需要它来制作我们的游戏。

{% hint style="success" %}
**Mirror** 在UNET的基础上需要超过1万小时的工作量。\
**UNET** 是在几年时间内开发的，很可能是在Unity的**传统**网络基础之上。\
**传统**网络可能是由我们甚至不认识的人开发了好几年。\
\
Mirror站在**巨人的肩膀**上。\
很难估计有多少年、多少工时、多少汗水、眼泪投入到这项技术中。
{% endhint %}
