---
title: Azure 服务总线常见问题解答 (FAQ) | Microsoft Docs
description: 解答一些有关 Azure 服务总线的常见问题。
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8a2a704f39aa678be819a7297b30f8926e414e56
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748446"
---
# <a name="service-bus-faq"></a>服务总线常见问题解答

本文讨论了一些关于 Microsoft Azure 服务总线的常见问题解答。 还可以访问 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)了解常规的 Azure 定价和支持信息。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>关于 Azure 服务总线的一般问题
### <a name="what-is-azure-service-bus"></a>什么是 Azure 服务总线？
[Azure 服务总线](service-bus-messaging-overview.md)是一个异步消息传送云平台，可允许在分离的系统之间发送数据。 Microsoft 以服务的形式提供此功能，这意味着不需要托管自有硬件就能使用它。

### <a name="what-is-a-service-bus-namespace"></a>什么是服务总线命名空间？
[命名空间](service-bus-create-namespace-portal.md)提供了用于对应用程序中的服务总线资源进行寻址的范围容器。 必须创建命名空间才能使用服务总线，而且这也是开始使用的第一步。

### <a name="what-is-an-azure-service-bus-queue"></a>什么是 Azure 服务总线队列？
[服务总线队列](service-bus-queues-topics-subscriptions.md)是用于存储消息的实体。 有多个应用程序，或者有多个需要彼此通信的分布式应用程序部分时，队列特别有用。 队列和发行中心的相似之处在于，两者都会接收多个产品（消息），再从该处送出。

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>什么是 Azure 服务总线主题和订阅？
主题可被视为队列，使用多个订阅时，它将成为更丰富的消息传送模型；实质上是一种一对多的通信工具。 此发布/订阅模型（或 pub/sub）启用了一个应用程序，该应用程序将消息发送到具有多个订阅的主题中，进而使多个应用程序接收到该消息。

### <a name="what-is-a-partitioned-entity"></a>什么是分区实体？
传统的队列或主题由单个消息中转站进行处理并存储在一个消息存储中。 仅在基本和标准消息传递层中受支持，[分区队列或主题](service-bus-partitioning.md)由多个消息中转站处理，并存储在多个消息传送存储中。 此功能意味着分区的队列或主题的总吞吐量不再受到单个消息中转站或消息存储的性能限制。 此外，消息传送存储的临时中断不会导致分区的队列或主题不可用。

使用分区实体时不保证排序。 如果某个分区不可用，仍可从其他分区发送和接收消息。

 [高级 SKU](service-bus-premium-messaging.md) 中不再支持分区实体。 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>我需要在防火墙上打开哪些端口？ 
可以将以下协议与 Azure 服务总线配合使用，以便发送和接收消息：

- 高级消息队列协议 (AMQP)
- 服务总线邮件协议 (SBMP)
- HTTP

请查看下表，了解需要打开哪些出站端口，以便使用这些协议与 Azure 事件中心通信。 

| 协议 | 端口 | 详细信息 | 
| -------- | ----- | ------- | 
| AMQP | 5671 和 5672 | 请参阅 [AMQP 协议指南](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 到 9354 | 请参阅[连接模式](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP、HTTPS | 80、443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>我需要将哪些 IP 地址加入允许列表？
若要找到适合加入连接的允许列表的 IP 地址，请执行以下步骤：

1. 从命令提示符处运行以下命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 记下在 `Non-authoritative answer` 中返回的 IP 地址。 此 IP 地址是静态的。 只有在你将命名空间还原到另一群集时，它才会更改。

如果对命名空间使用区域冗余，则需执行一些额外的步骤： 

1. 首先，在命名空间中运行 nslookup。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 记下“非权威回答”部分中的名称，该名称采用下述格式之一： 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. 为每一个运行 nslookup，使用后缀 s1、s2 和 s3 获取所有三个在三个可用性区域中运行的实例的 IP 地址。 


## <a name="best-practices"></a>最佳实践
### <a name="what-are-some-azure-service-bus-best-practices"></a>Azure 服务总线的最佳实践有哪些？
请参阅[使用服务总线改进性能的最佳实践][Best practices for performance improvements using Service Bus]-本文介绍如何在交换消息时优化性能。

### <a name="what-should-i-know-before-creating-entities"></a>创建实体前需了解哪些事项？
队列和主题的下列属性固定不变。 预配实体时，请考虑此限制，因为必须创建新的替代实体才可修改这些属性。

* 分区
* 会话
* 重复检测
* 快速实体

## <a name="pricing"></a>定价
本部分回答了一些关于服务总线定价结构的常见问题解答。

[服务总线定价和计费](https://azure.microsoft.com/pricing/details/service-bus/)译文介绍了服务总线中的计费标准。 有关服务总线定价选项的具体信息，请参阅[服务总线定价详细信息](https://azure.microsoft.com/pricing/details/service-bus/)。

还可以访问 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)了解常规的 Azure 定价信息。 

### <a name="how-do-you-charge-for-service-bus"></a>服务总线如何收取费用？
有关服务总线定价的完整信息，请参阅[服务总线定价][Pricing overview]。 除标示的价格外，还需为在其中部署应用程序的数据中心之外的相关数据输出支付费用。

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>服务总线的哪些使用情况受数据传输限制？ 哪些不受其限制？
免费提供给定 Azure 区域内的任意数据传输和入站数据传输。 区域外的数据传输需收取输出费用，详见[此处](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="does-service-bus-charge-for-storage"></a>服务总线是否对存储收费？
否，服务总线不对存储收费。 但是，对于每个队列/主题的最大可保留数据量，有配额限制。 请参阅下一个常见问题。

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>我有一个服务总线标准命名空间。 为什么在资源组 "$system" 下看不到收费？
Azure 服务总线最近升级了计费组件。 出于此原因，如果你有一个 Service Bus 标准命名空间，则可能会在资源组中看到资源 "/subscriptions/< azure_subscription_id >/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" 的行项系统 '。

这些费用表示预配了服务总线标准命名空间的每个 Azure 订阅的基本费用。 

务必要注意的是，这些不是新费用，也就是说，它们也存在于以前的计费模型中。 唯一的更改是它们现在列在 "$system" 下。 这是由于在 "$system" 资源 id 下，在新的计费系统中对订阅级别收费进行分组，而不是与特定资源相关联的约束。

## <a name="quotas"></a>配额

有关服务总线限制和配额的列表，请参阅[服务总线配额概述][Quotas overview]。

### <a name="does-service-bus-have-any-usage-quotas"></a>服务总线是否有任何使用率配额？
默认情况下，对于任何云服务，Microsoft 设置聚合的每月使用配额，通过对所有的客户订阅计算得到。 如果你需要更多配额，可以随时联系客户服务，以便了解你的需求并相应地调整这些限制。 对于服务总线，总用量配额是每月 50 亿条消息。

虽然 Microsoft 保留禁用在给定月份超过使用配额的客户帐户的权利，但仍然会在采取任何措施前发送电子邮件通知，并多次尝试与客户联系。 超过这些配额的客户仍需负责超出配额的费用。

至于 Azure 上的其他服务，服务总线会强制使用一组特定配额，以确保资源的公平使用。 可以在[服务总线配额概述][Quotas overview]中找到有关这些配额的更多详细信息。

### <a name="how-to-handle-messages-of-size--1-mb"></a>如何处理大小 > 1 MB 的消息？
服务总线消息服务（队列和主题/订阅）允许应用程序发送大小不超过 256 KB（标准层）或 1 MB（高级层）的消息。 若要处理大小超过 1 MB 的消息，请使用[此博客文章](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)中所述的声明检查模式。

## <a name="troubleshooting"></a>故障排除
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>为什么在从其他订阅中删除命名空间后无法创建该命名空间？ 
从订阅中删除命名空间时，请等待 4 个小时，然后才能在另一个订阅中使用相同的名称重新创建它。 否则，可能会收到以下错误消息：`Namespace already exists`。 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Azure 服务总线 API 生成了哪些异常？建议采取什么操作？
有关可能的服务总线异常的列表，请参阅[异常概述][Exceptions overview]。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>什么是共享访问签名？哪些语言支持生成签名？
共享访问签名是基于 SHA-256 安全哈希或 URI 的身份验证机制。 有关如何在 node.js、PHP、Java、Python 和C#中生成自己的签名的信息，请参阅[共享访问签名][Shared Access Signatures]一文。

## <a name="subscription-and-namespace-management"></a>订阅和命名空间管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何将命名空间迁移到另一个 Azure 订阅中？

可以使用 [Azure 门户](https://portal.azure.com)或 PowerShell 命令，将命名空间从一个 Azure 订阅移到另一个 Azure 订阅。 若要执行此操作，命名空间必须已处于活动状态。 执行这些命令的用户必须是源订阅和目标订阅的管理员。

#### <a name="portal"></a>门户

要使用 Azure 门户将服务总线命名空间迁移到其他订阅，可按照[此处](../azure-resource-manager/resource-group-move-resources.md#use-the-portal)的说明操作。 

#### <a name="powershell"></a>PowerShell

下面的 PowerShell 命令序列将命名空间从一个 Azure 订阅迁移到另一个 Azure 订阅。 若要执行此操作，该命名空间必须已经处于活动状态，并且运行 PowerShell 命令的用户必须同时是源和目标订阅上的管理员。

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>后续步骤
若要了解有关服务总线的详细信息，请参阅以下文章：

* [Azure 服务总线高级版简介（博客文章）](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服务总线高级版简介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服务总线概述](service-bus-messaging-overview.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
