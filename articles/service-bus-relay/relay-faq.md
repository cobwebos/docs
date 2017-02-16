---
title: "Azure 中继常见问题解答 (FAQ) | Microsoft Docs"
description: "回答了一些关于 Azure 中继的常见问题。"
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 22781ec965decbfd33923478794a76692151c363


---
# <a name="relay-faq"></a>中继常见问题解答
本文回答了一些关于 Microsoft Azure 中继的常见问题。 还可以访问 [Azure 支持常见问题](http://go.microsoft.com/fwlink/?LinkID=185083)了解一般的 Azure 定价和支持信息。

## <a name="general-questions"></a>一般问题
### <a name="what-is-azure-relay"></a>什么是 Azure 中继？
Azure [中继服务](relay-what-is-it.md)简化了混合应用程序，通过允许安全地向公有云公开位于企业网络内的服务，无需打开防火墙连接，也无需对企业网络基础结构进行彻底更改。

### <a name="what-is-a-relay-namespace"></a>什么是中继命名空间？
[命名空间](relay-create-namespace-portal.md)提供了用于对应用程序中的中继资源进行寻址的范围容器。 必须创建命名空间才能使用中继，而且这也是入门的第一步。

### <a name="what-happened-to-the-previously-named-relay-service"></a>以前命名的中继服务有什么变化？
以前命名的**中继**服务现在称为 *WCF 中继*。 你可以继续照常使用此服务。 混合连接是从 BizTalk 移植的服务的更新版本。 今后将继续支持 WCF 中继和混合连接。

## <a name="pricing"></a>定价
本部分回答了一些关于中继定价结构的常见问题。 你还可以访问 [Azure Support FAQ](http://go.microsoft.com/fwlink/?LinkID=185083)（Azure 支持常见问题）了解一般的 Microsoft Azure 定价信息。 有关中继定价的完整信息，请参阅[服务总线定价详细信息](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>如何对混合连接和 WCF 中继收费？
有关中继定价的完整信息，请参阅[服务总线定价详细信息][Pricing overview]。 除标示的价格外，你还需为在其中部署应用程序的数据中心之外的相关数据输出支付费用。

### <a name="how-am-i-billed-for-hybrid-connections"></a>使用混合连接时怎样计费？
以下是三个示例方案：

1. 如果你安装了一个侦听器（如一个混合连接管理器的实例）并连续运行一整月，且这个月内跨连接发送了 3GB 数据，那么总费用将为 5 美元。
2. 如果你安装了一个侦听器（如一个混合连接管理器的实例）并连续运行一整月，且这个月内跨连接发送了 10GB 数据，那么总费用将为 7.50 美元：5 美元（连接和前 5GB）+ 2.50 美元（额外的 5GB 数据）。
3. 如果你安装了两个混合连接管理器实例（A 和 B）并连续运行一整月，且这个月内跨连接 A 发送了 3GB 数据，跨连接 B 发送了 6GB 数据，那么总费用将为 10.50 美元：5 美元（连接 A）+ 5 美元（连接 B）+ 0.50 美元（连接 B 上的第六个 GB）。

请注意，示例中使用的价格仅适用于预览版期间，混合连接公开上市时的价格可能会有所变动。

### <a name="how-are-wcf-relay-hours-calculated"></a>如何计算 WCF 中继小时数？
将按每个服务总线中继处于“打开”状态的累积时间量对中继小时数收费。 当启用了中继的 WCF 服务（即“中继侦听器”）第一次连接到给定服务总线地址（服务命名空间 URL）时，中继将隐式实例化并在该地址中打开。 仅当最后一个侦听器从其地址断开连接时，该中继才会关闭。 因此，出于计费目的，在第一个中继侦听器连接到中继的服务总线地址到最后一个中继侦听器从该地址断开连接的这段时间内，该中继将被认为处于“打开”状态。

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>如果将多个侦听器连接到给定中继，会怎么样？
在某些情况下，单个中继可能会有多个连接的侦听器。 当至少有一个中继侦听器连接到中继时，该中继都就将被视为“打开”状态。 将其他侦听器添加到打开的中继时，将不会增加中继小时数。 连接到中继的中继发送方（调用或将消息发送至中继的客户端）数量，也不会对中继小时数的计算产生影响。

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>如何计算 WCF 中继的消息数？
**这仅适用于 WCF 中继，不适用于混合连接的成本**

一般情况下，会使用与上述相同的用于中转实体（队列、主题和订阅）的方法来计算中继的可计费消息。 但是，有几个明显的区别：

将消息发送到服务总线中继是指，将消息“全通式”发送至接收消息的中继侦听器，而不是发送至服务总线中继后再传递到中继侦听器。 因此，针对中继侦听器的请求-应答模式服务调用（最大 64 KB）将生成两条可计费消息：一条用于请求的可计费消息，一条用于应答（假设响应也 \<= 64 KB）的可计费消息。 这不同于使用队列在客户端和服务之间进行调谐。 对于后一种情况，相同的请求-应答模式需要一个发送到队列的请求，接着是从队列取消排队/从队列传递到服务，然后是发送至另一个队列的响应，最后从该队列取消排队/从该队列传递到客户端。 因此，使用相同大小 (\<= 64 KB) 的假定吞吐量，经过调谐的队列模式将生成四条可计费消息，此数量是使用中继实现相同模式计费数的两倍。 当然，使用队列来实现此模式好处更多，例如持久性和负载分级。 这些好处可能产生额外费用。

使用 netTCPRelay WCF 绑定打开的中继不将消息视为单条消息，而视为流经系统的数据流。 换而言之，只有发送方和侦听器可以识别使用此绑定发送/接收的单条分帧消息。 因此，对于使用 netTCPRelay 绑定的中继，所有数据都将被视为用于计算可计费消息的数据流。 在此示例中，服务总线将计算每 5 分钟通过单个中继发送或接收的数据总量，然后将该总量除以 64 KB，从而得出在该时间段所涉及中继的可计费消息数。

## <a name="quotas"></a>配额
| 配额名称 | 范围 | 类型 | 超出时的行为 | 值 |
| --- | --- | --- | --- | --- |
| 中继上的并发侦听器数 |实体 |静态 |系统将拒绝后续的附加连接请求，且调用代码将收到异常。 |25 |
| 并发中继侦听器数 |系统范围 |静态 |系统将拒绝后续的附加连接请求，且调用代码将收到异常。 |2,000 |
| 服务命名空间中所有中继终结点的并发中继连接数 |系统范围 |静态 |- |5,000 |
| 每个服务命名空间的中继终结点数 |系统范围 |静态 |- |10,000 |
| [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) 和 [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) 中继的消息大小 |系统范围 |静态 |将拒绝超过这些配额的传入消息，且调用代码会收到异常。 |64KB |
| [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) 和 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 中继的消息大小 |系统范围 |静态 |- |不受限制 |

### <a name="does-relay-have-any-usage-quotas"></a>中继是否具有任何使用率配额？
默认情况下，对于任何云服务，Microsoft 设置聚合的每月使用配额，通过对所有的客户订阅计算得到。 由于我们清楚你可能需要这些限制之外的更多限制，因此，请随时联系客户服务，以便我们了解你的需求并相应地调整这些限制。 对于服务总线，聚合的使用率配额为如下所示：

* 50 亿条消息
* 200 万个中继小时

虽然我们保留禁用在给定月份超过使用率配额的客户帐户的权利，但我们仍然会在采取任何措施前发送电子邮件通知并会多次尝试与客户联系。 超过这些配额的客户仍将负责超出配额的费用。

### <a name="naming-restrictions"></a>命名限制
中继命名空间名称长度必须为 6-50 个字符。

## <a name="subscription-and-namespace-management"></a>订阅和命名空间管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何将命名空间迁移到另一个 Azure 订阅中？
你可以使用 PowerShell 命令（可在[此处](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription)的文章中找到）将命名空间从某个 Azure 订阅移到另一个 Azure 订阅。 若要执行此操作，命名空间必须已处于活动状态。 此外，执行这些命令的用户必须是源订阅和目标订阅的管理员。

## <a name="troubleshooting"></a>故障排除
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Azure 中继 API 所生成的异常有哪些，建议采取什么操作？
[中继异常][Relay exceptions]一文介绍了部分异常和建议的操作。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>什么是共享访问签名？哪些语言支持生成签名？
共享访问签名是基于 SHA–256 安全哈希或 URI 的身份验证机制。 有关如何在 Node、PHP、Java 和 C\# 中生成自有签名的信息，请参阅[共享访问签名][Shared Access Signatures]一文。

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas-overview.md

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>是否可以将中继终结点加入白名单？
是的。 中继客户端使用完全限定的域名连接到中继服务。 这使客户能够在支持 DNS 白名单的防火墙上为 `*.servicebus.windows.net` 添加一个条目。

## <a name="next-steps"></a>后续步骤
* [创建命名空间](relay-create-namespace-portal.md)
* [.NET 入门](relay-hybrid-connections-dotnet-get-started.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)



<!--HONumber=Jan17_HO4-->


