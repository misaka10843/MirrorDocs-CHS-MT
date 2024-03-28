# 延迟模拟传输(Latency Simulation Transport)

Mirror的延迟模拟传输允许您在非理想的网络条件下测试项目。

将其添加到NetworkManager中，**包裹**在常规传输周围，将其拖放到NetworkManager.transport中。

它可以模拟：

- 毫秒级的延迟
- 丢包率（%）
- 数据包混乱/重排

{% hint style="info" %}
可靠消息是按顺序且通过定义保证传递的。在可靠传输中的数据包丢失/混乱通过延迟来体现。&#x20;
{% endhint %}

![](../../.gitbook/assets/2021-03-14\_21-32-23@2x.png)

![NMLatencySim](https://user-images.githubusercontent.com/57072365/225437999-0667da1b-abf7-49c5-8c1d-d1a2fec36b12.jpg) (原文: NMLatencySim)
