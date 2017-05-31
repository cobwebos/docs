---
title: "Azure 服务总线常见问题解答 (FAQ) | Microsoft Docs"
description: "回答了一些关于 Azure 服务总线的常见问题。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2017
ms.author: sethm;jotaub
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 612fb681ab0c8fe77652041f8a1fc23ad0d3428a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="service-bus-faq"></a>服务总线常见问题解答
本文回答了一些关于 Microsoft Azure 服务总线的常见问题。 还可以访问 [Azure 支持常见问题](http://go.microsoft.com/fwlink/?LinkID=185083)了解一般的 Azure 定价和支持信息。

## <a name="general-questions-about-azure-service-bus"></a>关于 Azure 服务总线的一般问题
### <a name="what-is-azure-service-bus"></a>什么是 Azure 服务总线？
[Azure 服务总线](service-bus-messaging-overview.md)是一个异步消息传送云平台，可允许在分离的系统之间发送数据。 Microsoft 以服务的形式提供此功能，这表示你不需要托管任何自有硬件就能使用它。

### <a name="what-is-a-service-bus-namespace"></a>什么是服务总线命名空间？
[命名空间](service-bus-create-namespace-portal.md)提供了用于对应用程序中的服务总线资源进行寻址的范围容器。 必须创建命名空间才能使用服务总线，而且这也是入门的第一步。

### <a name="what-is-an-azure-service-bus-queue"></a>什么是 Azure 服务总线队列？
[服务总线队列](service-bus-queues-topics-subscriptions.md)是用于存储消息的实体。 当你有多个应用程序，或者多个需要彼此通信的分布式应用程序部分时，队列特别有用。 队列和发行中心的相似之处在于，两者都会接收多个产品（消息），再从该处送出。

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>什么是 Azure 服务总线主题和订阅？
主题可被视为队列，使用多个订阅时，它将成为更丰富的消息传送模型；实质上是一种一对多的通信工具。 此发布/订阅模型（或 pub/sub）启用了一个应用程序，该应用程序将消息发送到具有多个订阅的主题中，进而使多个应用程序接收到该消息。

### <a name="what-is-a-partitioned-entity"></a>什么是分区实体？
传统的队列或主题由单个消息中转站进行处理并存储在一个消息存储中。 [分区队列或主题](service-bus-partitioning.md)由多个消息中转站处理，并存储在多个消息传送存储中。 这意味着分区的队列或主题的总吞吐量不再受到单个消息中转站或消息存储的性能所限制。 此外，消息传送存储的临时中断不会导致分区的队列或主题不可用。

请注意，使用分区实体时不保证排序。 如果某个分区不可用，你仍可从其他分区发送和接收消息。

## <a name="best-practices"></a>最佳实践
### <a name="what-are-some-azure-service-bus-best-practices"></a>Azure 服务总线的最佳实践有哪些？
* [使用服务总线改进性能的最佳实践][Best practices for performance improvements using Service Bus] - 本文将说明如何在交换消息时优化性能。

### <a name="what-should-i-know-before-creating-entities"></a>创建实体前需了解哪些事项？
队列和主题的下列属性固定不变。 预配实体时请考虑到这一点，因为若要修改属性，就必须创建新的替代实体。

* 大小
* 分区
* 会话
* 重复检测
* 快速实体

## <a name="pricing"></a>定价
本部分回答了一些关于服务总线定价结构的常见问题。

[服务总线定价和计费](service-bus-pricing-billing.md)一文介绍了服务总线中的计费计量，有关服务总线定价选项的信息，请参阅[服务总线定价详细信息](https://azure.microsoft.com/pricing/details/service-bus/)。

还可以访问 [Azure Support FAQ](http://go.microsoft.com/fwlink/?LinkID=185083)（Azure 支持常见问题），了解一般的 Azure 定价信息。 

### <a name="how-do-you-charge-for-service-bus"></a>服务总线如何收取费用？
有关服务总线定价的完整信息，请参阅[服务总线定价详细信息][Pricing overview]。 除标示的价格外，你还需为在其中部署应用程序的数据中心之外的相关数据输出支付费用。

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>服务总线的哪些使用情况受数据传输限制？ 哪些不受其限制？
免费提供给定 Azure 区域内的任意数据传输和入站数据传输。 区域外的数据传输需收取输出费用，详见[此处](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="does-service-bus-charge-for-storage"></a>服务总线是否对存储收费？
否，服务总线不对存储收费。 但是，对于每个队列/主题的最大可保留数据量，有配额限制。 请参阅下一个常见问题。

## <a name="quotas"></a>配额

有关服务总线限制和配额的列表，请参阅[服务总线配额概述][Quotas overview]。

### <a name="does-service-bus-have-any-usage-quotas"></a>服务总线是否有任何使用率配额？
默认情况下，对于任何云服务，Microsoft 设置聚合的每月使用配额，通过对所有的客户订阅计算得到。 由于我们清楚你可能需要这些限制之外的更多限制，因此，请随时联系客户服务，以便我们了解你的需求并相应地调整这些限制。 对于服务总线，总用量配额是每月 50 亿条消息。

虽然我们保留禁用在给定月份超过使用率配额的客户帐户的权利，但我们仍然会在采取任何措施前发送电子邮件通知并会多次尝试与客户联系。 超过这些配额的客户仍将负责超出配额的费用。

至于 Azure 上的其他服务，服务总线会强制使用一组特定配额，以确保资源的公平使用。 可以在[服务总线配额概述][Quotas overview]中找到有关这些配额的更多详细信息。

## <a name="troubleshooting"></a>故障排除
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Azure 服务总线 API 生成了哪些异常？建议采取什么操作？
有关可能的服务总线异常的列表，请参阅[异常概述][Exceptions overview]。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>什么是共享访问签名？哪些语言支持生成签名？
共享访问签名是基于 SHA–256 安全哈希或 URI 的身份验证机制。 有关如何在 Node、PHP、Java 和 C\# 中生成自有签名的信息，请参阅[共享访问签名][Shared Access Signatures]一文。

## <a name="subscription-and-namespace-management"></a>订阅和命名空间管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何将命名空间迁移到另一个 Azure 订阅中？

可以使用 [Azure 门户](https://portal.azure.com)或 PowerShell 命令，将命名空间从一个 Azure 订阅移到另一个 Azure 订阅。 若要执行此操作，命名空间必须已处于活动状态。 执行这些命令的用户必须是源订阅和目标订阅的管理员。

#### <a name="portal"></a>门户

若要使用 Azure 门户将服务总线命名空间迁移到其他订阅，可按照[此处](../azure-resource-manager/resource-group-move-resources.md#use-portal)的说明操作。 

#### <a name="powershell"></a>PowerShell

下面的 PowerShell 命令序列将命名空间从一个 Azure 订阅迁移到另一个 Azure 订阅。 若要执行此操作，该命名空间必须已经处于活动状态，并且运行 PowerShell 命令的用户必须同时是源和目标订阅上的管理员。

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>后续步骤
若要了解有关服务总线的详细信息，请参阅以下主题。

* [Azure 服务总线高级版简介（博客文章）](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服务总线高级版简介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服务总线概述](service-bus-messaging-overview.md)
* [Azure 服务总线体系结构概述](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md

