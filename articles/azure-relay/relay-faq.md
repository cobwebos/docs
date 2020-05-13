---
title: Azure 中继常见问题 | Microsoft Docs
description: 本文提供了一些有关 Azure 中继服务的常见问题解答 (FAQ)。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: ac8967ab036929bf5363a79c9d7c60485bc98841
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211978"
---
# <a name="azure-relay-faqs"></a>Azure 中继常见问题解答

本文解答一些关于 [Azure 中继](https://azure.microsoft.com/services/service-bus/)的常见问题 (FAQ)。 若要了解一般的 Azure 定价和支持信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>一般问题
### <a name="what-is-azure-relay"></a>什么是 Azure 中继？
[Azure 中继服务](relay-what-is-it.md)可以帮助你以更安全的方式将企业网络中驻留的服务公开给公有云，从而改进混合应用程序的使用。 可以在不打开防火墙连接的情况下公开服务，不需对企业网络基础结构进行干预性更改。

### <a name="what-is-a-relay-namespace"></a>什么是中继命名空间？
[命名空间](relay-create-namespace-portal.md)是一个范围容器，可用于对应用程序中的中继资源进行寻址。 必须创建命名空间才能使用中继。 这是入门的开始步骤之一。

### <a name="what-happened-to-service-bus-relay-service"></a>服务总线中继服务发生了什么情况？
以前命名的服务总线中继服务现在称为[Azure 中继](service-bus-relay-tutorial.md)。 可以继续照常使用此服务。 混合连接功能是一种更新版的服务，从 Azure BizTalk 服务移植过来。 将继续支持 WCF 中继和混合连接。

## <a name="pricing"></a>定价
本部分回答了一些关于中继定价结构的常见问题。 若要了解一般的 Azure 定价信息，还可以参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。 有关中继定价的完整信息，请参阅[服务总线定价详细信息][Pricing overview]。

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>如何对混合连接和 WCF 中继收费？
有关中继定价的完整信息，请参阅服务总线定价详细信息页上的[混合连接和 WCF 中继][Pricing overview]表。 除该页上标示的价格外，还需为在其中部署应用程序的数据中心之外的相关数据输出支付费用。

### <a name="how-am-i-billed-for-hybrid-connections"></a>使用混合连接时怎样计费？
以下是三种示例计费方案，适用于混合连接：

*   应用场景 1：
    *   有一个侦听器，例如一个混合连接管理器实例，在安装后已持续运行一整月。
    *   在该月通过连接发送了 3 GB 的数据。 
    *   总费用是 5 美元。
*   应用场景 2：
    *   有一个侦听器，例如一个混合连接管理器实例，在安装后已持续运行一整月。
    *   在该月通过连接发送了 10 GB 的数据。
    *   总费用是 7.50 美元。 其中 5 美元是针对连接和第一个 5 GB 的收费，另外 2.50 美元是针对其余 5 GB 数据的收费。
*   应用场景 3：
    *   有两个混合连接管理器实例（A 和 B），在安装后已持续运行一整月。
    *   在该月通过连接 A 发送了 3 GB 的数据。
    *   在该月通过连接 B 发送了 6 GB 的数据。
    *   总费用是 10.50 美元。 其中 5 美元是针对连接 A 的收费，5 美元是针对连接 B 的收费，还有 0.50 美元是针对连接 B 额外的 1 GB 数据的收费。

请注意，示例中所使用的价格仅适用于混合连接预览期。 在公开发布混合连接后，价格可能会变化。

### <a name="how-are-hours-calculated-for-relay"></a>如何计算中继小时数？

WCF 中继仅适用于标准层命名空间。 其他中继的定价和[连接配额](../service-bus-messaging/service-bus-quotas.md)保持不变。 这意味着中继将继续根据消息数（而不是操作数）和中继小时数计费。 有关详细信息，请参阅定价详细信息页上的[混合连接和 WCF 中继](https://azure.microsoft.com/pricing/details/service-bus/)表。

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>如果将多个侦听器连接到特定中继，会怎么样？
在某些情况下，单个中继可能会有多个连接的侦听器。 当至少有一个中继侦听器连接到中继时，该中继都就会被视为“打开”状态。 将侦听器添加到打开的中继时，会增加中继小时数。 连接到中继的中继发送方（调用消息或将消息发送至中继的客户端）数量不会对中继小时数的计算产生影响。

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>如何计算 WCF 中继的消息数？
（**这仅适用于 WCF 中继。对于混合连接来说，消息不收费。** ）

一般情况下，会使用与上述相同的用于中转实体（队列、主题和订阅）的方法来计算中继的可计费消息。 但是，有一些明显的区别。

将消息发送到 Azure 中继将被视为 "完全通过" 发送到接收消息的中继侦听器。 它不会被视为对 Azure 中继的发送操作，而是传递到中继侦听器。 针对中继侦听器的请求-应答模式服务调用（最大 64 KB）将生成两条可计费消息：一条用于请求的可计费消息，一条用于应答（假设响应也是 64 KB 或更小）的可计费消息。 这不同于使用队列在客户端和服务之间进行协调。 如果使用队列在客户端和服务之间进行协调，则同一请求-答复模式要求先将请求发送到队列，然后再将其从队列交付到服务，或者取消其排队。 随后就是将响应发送至另一队列，再从该队列交付至客户端，或者取消排队。 如果始终使用同一大小作为假设吞吐量（最高 64 KB），则此中介型队列模式会生成 4 条计费消息。 收费的消息数两倍于使用中继实现同一模式时的消息数。 当然，使用队列来实现此模式好处更多，例如持久性和负载分级。 这些好处可能产生额外费用。

使用 **netTCPRelay** WCF 绑定打开的中继不将消息视为单条消息，而视为流经系统的数据流。 使用此绑定时，只有发送方和侦听器可以识别发送和接收的单条分帧消息。 对于使用 **netTCPRelay** 绑定的中继，所有数据都会被视为用于计算可计费消息的数据流。 在这种情况下，服务总线每隔 5 分钟计算一次通过单个中继发送或接收的数据总量。 然后会将该数据总量除以 64 KB，得出该中继在该时段的计费消息数。

## <a name="quotas"></a>配额
| 配额名称 | 作用域 |  注释 | Value |
| --- | --- | --- | --- |
| 中继上的并发侦听器数 |实体 |系统会拒绝后续的附加连接请求，且调用代码会收到异常。 |25 |
| 服务命名空间中所有中继终结点的并发中继连接数 |命名空间 |- |5,000 |
| 每个服务命名空间的中继终结点数 |命名空间 |- |10,000 |
| [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) 和 [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) 中继的消息大小 |命名空间 |会拒绝超过这些配额的传入消息，且调用代码会收到异常。 |64 KB |
| [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) 和 [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) 中继的消息大小 |命名空间 |对消息大小没有限制。 |无限制 |

### <a name="does-relay-have-any-usage-quotas"></a>中继是否具有任何使用率配额？
默认情况下，对于任何云服务，Microsoft 设置聚合的每月使用配额，通过对所有的客户订阅计算得到。 我们了解，有时候，你的需求可能会超过这些限制。 你可以随时联系客户服务人员，以便我们了解你的需求并相应地调整这些限制。 对于服务总线，聚合的使用率配额为如下所示：

* 50 亿条消息
* 200 万个中继小时

虽然我们保留禁用超过单月使用率配额的帐户的权利，但仍然会在采取任何措施前发送电子邮件通知并会多次尝试与客户联系。 超过这些配额的客户仍需负责支付超额部分的费用。

### <a name="naming-restrictions"></a>命名限制
中继命名空间名称的长度必须介于 6 到 50 个字符之间。

## <a name="subscription-and-namespace-management"></a>订阅和命名空间管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何将命名空间迁移到另一个 Azure 订阅中？

可以使用 [Azure 门户](https://portal.azure.com)或 PowerShell 命令，将命名空间从一个 Azure 订阅移到另一个订阅。 若要将命名空间移到另一个订阅，该命名空间必须处于活动状态。 运行这些命令的用户必须是源订阅和目标订阅的管理员用户。

#### <a name="azure-portal"></a>Azure 门户

要使用 Azure 门户将 Azure 中继命名空间从一个订阅迁移到另一个订阅，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)。 

#### <a name="powershell"></a>PowerShell

若要使用 PowerShell 将命名空间从一个 Azure 订阅移到另一个订阅，请使用以下命令序列。 若要执行此操作，该命名空间必须已经处于活动状态，并且运行 PowerShell 命令的用户必须同时是源和目标订阅上的管理员用户。

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>故障排除
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Azure 中继 API 所生成的异常有哪些，可以采用哪些建议操作？
有关常见异常以及可以采用的建议操作的说明，请参阅[中继异常][Relay exceptions]。

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>什么是共享访问签名？哪些语言可以用来生成签名？
共享访问签名 (SAS) 是基于 SHA–256 安全哈希或 URI 的身份验证机制。 有关如何在 Node.js、PHP、Python、Java、C 和 C# 中生成自有签名的信息，请参阅[使用共享访问签名进行服务总线身份验证][Shared Access Signatures]。

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>是否可以将中继终结点加入允许列表？
是的。 中继客户端使用完全限定的域名连接到 Azure 中继服务。 客户可以在支持 DNS 允许列表的防火墙上为 `*.servicebus.windows.net` 添加一个条目。

## <a name="next-steps"></a>后续步骤
* [创建命名空间](relay-create-namespace-portal.md)
* [.NET 入门](relay-hybrid-connections-dotnet-get-started.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
