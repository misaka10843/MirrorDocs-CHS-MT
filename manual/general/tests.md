---
description: Mirror 秘密酱。
---

# 测试

许多开发人员对 Mirror 的稳定性感到惊讶，与他们以前使用的相比。

这并非巧合。Mirror 经过**大量**测试：

* \> **1400** 单元测试
* \~ **80%** 测试覆盖率

![\[2021-06-17\] Mirror 测试覆盖率为 79.6% - 包括所有\[已弃用\]](<../../.gitbook/assets/2021-06-17 - 79,6 percent - including obsoletes.png>)

{% hint style="success" %}
据我们所知，**Mirror** 在 **Unity** 中是任何 `MonoBehaviour` 网络库中测试覆盖率最高的。&#x20;
{% endhint %}

换句话说，我们的代码中有 80% 被测试覆盖，确保对于给定的输入，它总是产生正确的输出。这在实践中意味着什么：

*   如果您**报告了一个 bug**，我们通常会修复它并添加一个测试来确保它**永远**不会再次发生。&#x20;

    如果我们**意外**引入了一个 bug，很可能我们的测试会立即捕获到它，而您在游戏中永远不会遇到它。
* 我们可以**有信心地改进**现有功能。如果重写的代码与之前的版本产生的输出不完全相同，那么我们的测试会捕获到它。

{% hint style="success" %}
作为**经验法则**，在生产中遇到 Mirror 的 bug 简单地意味着我们尚未用测试覆盖代码的那部分。
{% endhint %}

![](../../.gitbook/assets/2021-05-20\_16-06-57@2x.png)

如果您从 **Asset Store** 下载 Mirror，那么您看不到这些测试，因为我们不希望您为此担心。它们只在 **GitHub** 上。

## 代码覆盖设置

要重现覆盖率结果，请使用 Unity 的代码覆盖包并运行我们所有的编辑模式测试。

![代码覆盖设置](../../.gitbook/assets/\_SETTINGS\_.png)

## MirrorTest

如果您想要贡献测试或清理现有测试，请随时！

查看 `MirrorEditModeTest` 和 `MirrorPlayModeTest` 基类。它们提供了一些我们用于大多数测试的便利函数和设置。例如，创建一个带有一些网络组件的网络对象。