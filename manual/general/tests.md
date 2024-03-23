---
描述：镜秘酱。
---

# 测试

许多开发人员对 Mirror 的稳定性感到惊讶，与他们以前使用的相比。

这不是巧合。 Mirror 经过**严格**测试：

- \>**1400 个**单元测试
- \~**80%**测试覆盖率

![[2021-06-17]Mirror测试覆盖率达79.6% -包括所有[过时]](<../../.gitbook/assets/2021-06-17 - 79,6 percent - including obsoletes.png>)

{% hint style="success"%}
据我们所知，**Mirror\*\***在 Unity\*\*的任何`MonoBehaviour`网络库中具有最高的测试覆盖率。x20;
联系我们

换句话说，我们 80%的代码都**被测试覆盖，**以确保对于给定的输入，它总是产生正确的输出。 这在实践中意味着：

- 如果你**报告了一个 bug**，我们通常会修复它并添加一个测试来保证它**不会**再次发生。&# x20;

  If we **accidentally** introduce a bug, odds are our tests will catch it immediately before you ever encounter it in our game.

- 我们可以满怀信心**地改进**现有功能。 如果重写产生的输出与前一个版本不完全相同，那么我们的测试将捕获它。

{% hint style="success"%}
**根据\*\***经验**，在生产环境中遇到 Mirror 错误仅仅意味着我们还没有用测试覆盖这部分代码。\*\***
联系我们

![](../../.gitbook/assets/2021-05-20_16-06-57@2x.png)

如果您从**Asset Store**下载 Mirror，则不会看到这些测试，因为我们不希望您担心它们。 它们只在**GitHub**上。

## 代码覆盖率设置

要重现覆盖率结果，请使用 Unity 的代码覆盖率包并运行所有编辑模式测试。

![代码覆盖率设置](../../.gitbook/assets/_SETTINGS_.png)

## 测试报告

如果您想贡献测试或清理现有的测试，请这样做！

请查看`MirrorEditModeTest`和`MirrorPlayModeTest`基类。 它们提供了一些方便的功能和设置，我们在大多数测试中使用。 例如，创建具有某些网络组件的网络对象。
