---
title: Azure 服务总线异地灾难恢复 | Microsoft 文档
description: 如何使用地理区域进行故障转移并在 Azure 服务总线中执行灾难恢复
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/14/2018
ms.author: sethm
ms.openlocfilehash: b43c5bd6ff6b386e1a2ee0b5e3ae8ec8fa61fb4b
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301513"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure 服务总线异地灾难恢复

当整个 Azure 区域或数据中心（如果未使用[可用性区域](../availability-zones/az-overview.md)）遭遇停机时，在不同区域或数据中心中继续进行数据处理就显得至关重要。 在这种情况下，异地灾难恢复和异地复制对于任何企业而言都是至关重要的功能。 Azure 服务总线支持命名空间级别的异地灾难恢复和异地复制。 

异地灾难恢复功能在全球范围内可用于服务总线高级 SKU。 

## <a name="outages-and-disasters"></a>中断和灾难

请务必注意“中断”和“灾难”的区别。 中断是指 Azure 服务总线暂时不可用，可能会影响服务的某些组件，如消息存储，甚至是整个数据中心。 但在问题解决后，服务总线将恢复可用。 通常情况下，中断不会导致消息或其他数据丢失。 例如，数据中心的电源故障可能会导致此类中断。 某些中断由于暂时性故障或网络问题只是短时间连接丢失。 

根据定义，*灾难*是指永久或长期丢失服务总线群集、Azure 区域或数据中心。 该区域或数据中心不一定会恢复可用，或可能停用数小时或数天。 例如，火灾、洪灾或地震等可能导致此类灾难。 永久性灾难可能会导致一些消息、事件或其他数据丢失。 不过，在大多数情况下，都不应该会有数据丢失，并且在数据中心备份后，便可以恢复消息。

Azure 服务总线的异地灾难恢复功能是一项面向灾难恢复的解决方案。 本文中所述的概念和工作流适用于灾难方案，而不适用于暂时或临时中断。 有关 Microsoft Azure 中的灾难恢复的详细讨论，请参阅[此文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。   

## <a name="basic-concepts-and-terms"></a>基本概念和术语

灾难恢复功能可实现元数据灾难恢复，并且依赖于主要和次要灾难恢复命名空间。 请注意，异地灾难恢复功能仅适用于[高级 SKU](service-bus-premium-messaging.md)。 不需要对连接字符串进行任何更改，因为连接是通过别名建立的。

本文涉及以下术语：

-  *别名*：所设置的灾难恢复配置的名称。 别名提供单个稳定的完全限定域名 (FQDN) 连接字符串。 应用程序使用此别名连接字符串连接到命名空间。 

-  *主要/次要命名空间*：别名所对应的命名空间。 主要命名空间是“主动”的，并且会接收消息（该命名空间可以是现有的命名空间，也可以是新的命名空间）。 次要命名空间是“被动”的，不接收消息。 这两者之间的元数据保持同步，因此这两者可以无缝接受消息，而不必更改任何应用程序代码或连接字符串。 若要确保只有主动命名空间接收消息，必须使用别名。 

-  *元数据*：队列、主题、订阅等实体及其与命名空间关联的服务的属性。 请注意，仅自动复制实体及其设置。 不会复制消息。 

-  故障转移：激活辅助命名空间的过程。

## <a name="setup-and-failover-flow"></a>设置和故障转移流程

以下部分概述了故障转移流程，并说明了如何设置初始故障转移。 

![1][]

### <a name="setup"></a>设置

首先，创建一个主要命名空间或使用现有的主要命名空间，并创建一个新的次要命名空间，然后将两者配对。 此配对提供可用于连接的别名。 由于使用别名，因此，无需更改连接字符串。 只有新的命名空间可以添加到故障转移配对。 最后，应添加一些监视功能，以检测是否有必要进行故障转移。 在大多数情况下，服务是一个大型生态系统的一部分，因此极少可能会发生自动故障转移，因为通常故障转移必须在与剩余子系统或基础结构保持同步的情况下进行。

### <a name="example"></a>示例

在此方案的一个示例中，请考虑发出消息或事件的销售点 (POS) 解决方案。 服务总线将这些事件传递给某个映射或重格式化解决方案，该解决方案然后将映射的数据转发到另一个系统进一步处理。 此时，所有这些系统可能托管在同一 Azure 区域中。 至于何时进行故障转移以及对哪部分进行故障转移，则取决于基础结构中的数据流。 

可以使用监视系统或定制监视解决方案自动执行故障转移。 但是，这种自动执行需要额外的规划和工作，它超出了本文的讨论范围。

### <a name="failover-flow"></a>故障转移流程

如果启动故障转移，则需要两个步骤：

1. 如果又出现其他中断，则需要能够再次进行故障转移。 因此，请设置另一个被动命名空间，并更新配对。 

2. 一旦以前的主要命名空间恢复可用，请从该命名空间拉取消息。 在此之后，请使用该命名空间在异地恢复设置之外进行常规消息传送，或删除旧的主要命名空间。

> [!NOTE]
> 仅支持失败转发语义。 在此方案中，先进行故障转移，然后与新的命名空间重新配对。 不支持故障回复；例如，在 SQL 群集中不支持这样做。 

![2][]

## <a name="management"></a>管理

如果出了错，例如在初始设置过程中将错误的区域配对，则随时可以中断这两个命名空间的配对。 如果想要使用配对命名空间作为常规命名空间，请删除别名。

## <a name="use-existing-namespace-as-alias"></a>使用现有的命名空间作为别名

如果遇到不能更改生产者和使用者连接的情况，则可将命名空间名称作为别名重用。 请参阅[此处提供的 GitHub 上的示例代码](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)。

## <a name="samples"></a>示例

[GitHub 上的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/)演示如何设置和启动故障转移。 这些示例演示以下概念：

- 在 Azure Active Directory 中将 Azure 资源管理器与服务总线配合使用所需的 .NET 示例和设置，用来设置和启用异地灾难恢复。
- 执行示例代码所需的步骤。
- 如何使用现有的命名空间作为别名。
- 改用 PowerShell 或 CLI 启用异地灾难恢复的步骤。
- 使用别名从当前的主要或次要命名空间进行[发送和接收](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

## <a name="considerations"></a>注意事项

此版本需要注意以下事项：

1. 在故障转移规划中，还应考虑时间因素。 例如，如果失去连接的时间超过 15 到 20 分钟，你可能会决定启动故障转移。 
 
2. 未复制数据是指未复制当前处于活动状态的会话。 此外，重复检测和计划消息可能无法正常工作。 新会话、新计划消息和新的重复项可以正常工作。 

3. 故障转移复杂的分布式基础结构应至少[演练](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation)一次。 

4. 同步实体可能需要一些时间，每分钟大约 50-100 个实体。 订阅和规则也计为实体。 

## <a name="availability-zones-preview"></a>可用性区域（预览版）

服务总线高级 SKU 还支持[可用性区域](../availability-zones/az-overview.md)，在 Azure 区域内提供故障隔离位置。 

> [!NOTE]
> 仅在“美国中部”、“美国东部 2”和“法国中部”区域中支持可用性区域预览版。

可以使用 Azure 门户仅在新的命名空间上启用可用性区域。 服务总线不支持迁移现有命名空间。 在命名空间上启用区域冗余之后，不能将其禁用。

![3][]

## <a name="next-steps"></a>后续步骤

- 请参阅此处的异地灾难恢复 [REST API 参考](/rest/api/servicebus/disasterrecoveryconfigs)。
- 在 GitHub 上运行异地灾难恢复[示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)。
- 请参阅异地灾难恢复[将消息发送到别名的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

若要了解有关服务总线消息传送的详细信息，请参阅以下文章：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png