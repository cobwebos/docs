---
title: 自动转发 Azure 服务总线消息实体
description: 本文介绍如何将 Azure 服务总线队列或订阅链接到另一个队列或主题。
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8f5f93f65871c0b9658a75264ab959dbae7fefe7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819570"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>使用自动转发链接服务总线实体

通过服务总线自动转发功能可将队列或订阅链接到作为相同命名空间组成部分的另一个队列或主题。 启用自动转发时，服务总线会自动删除放置在第一个队列或订阅（源）中的消息，并将其放入第二个队列或主题（目标）中。 仍可将消息直接发送到目标实体。

## <a name="using-autoforwarding"></a>使用自动转发

可通过在源的 [QueueDescription][QueueDescription] 或 [SubscriptionDescription][SubscriptionDescription] 对象上设置 [QueueDescription.ForwardTo][QueueDescription.ForwardTo] 或 [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] 属性来启用自动转发，如以下示例所示：

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

创建源实体时，目标实体必须存在。 如果目标实体不存在，则当创建源实体时，服务总线返回异常。

可使用自动转发扩大单个主题。 服务总线将[给定主题的订阅数](service-bus-quotas.md)限制为 2,000。 可以通过创建二级主题来容纳其他订阅。 即使订阅数并未受到服务总线限制，添加二级主题也可以提高主题的整体吞吐量。

![自动转发方案的关系图，显示通过订单主题处理的消息，该主题可分支到三个二级订单主题中的任意一个。][0]

自动转发还可用于分离消息发送方与接收方。 例如，考虑一个由以下三个模块组成的 ERP 系统：订单处理、库存管理和客户关系管理。 每个模块都会生成消息，这些消息将被排入相应的主题队。 Alice 和 Bob 是两名销售代表，他们想了解与其客户相关的所有消息。 要接收这些消息，Alice 和 Bob 各自创建一个个人队列和一个针对 ERP 主题的订阅，该订阅将所有消息自动转发给该队列。

![自动转发方案示意图，其中显示了三个处理模块，通过三个相关主题将消息发送到两个单独的队列。][1]

如果 Alice 去度假，则填充她的个人队列，而不是 ERP 主题。 在此方案中，由于销售代表没有接收到任何消息，所以所有 ERP 主题都没有达到配额。

> [!NOTE]
> 设置自动转发时，**源和目标**上的 AutoDeleteOnIdle 值自动设置为数据类型的最大值。
> 
>   - 在源端，自动转发充当接收操作。 因此，具有自动转发设置的源从未真正“空闲”。
>   - 在目标端，这样做是为了确保始终有要将消息转发到的目标。

## <a name="autoforwarding-considerations"></a>自动转发注意事项

如果目标实体累积了过多消息并超出配额，或禁用了目标实体，则源实体会将消息添加到其[死信队列](service-bus-dead-letter-queues.md)，直到目标中存在可用空间（或重新启用了该实体）。 这些消息将继续位于死信队列中，因此，必须从死信队列显式接收和处理它们。

将单个主题链接到一起以获取包含许多订阅的复合主题时，建议设置适量的一级主题订阅和许多二级主题订阅。 例如，一个包含 20 个订阅的一级主题（其中每个订阅都链接到包含 200 个订阅的二级主题）就能够比包含 200 个订阅的一级主题（其中每个订阅链接到包含 20 个订阅的二级主题）具有更高的吞吐量。

服务总线对于每条转发的消息收取一个操作的费用。 例如，将一条消息发送到一个包含 20 个订阅（每个订阅配置为将消息自动转发到另一队列或主题）的主题，如果所有第一级别的订阅都接收到此消息的副本，则会作为 21 次操作进行计费。

若要创建链接到另一个队列或主题的订阅，则订阅创建者必须具有源和目标实体的**管理**权限。 将消息发送到源主题仅需要源主题的**发送**权限。

请勿创建超过4个跃点的链。 超过4个跃点的消息将死信。

## <a name="next-steps"></a>后续步骤

有关自动转发的详细信息，请参阅以下参考主题：

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

若要深入了解服务总线性能提升，请参阅 

* [使用服务总线消息传递改进性能的最佳实践](service-bus-performance-improvements.md)
* [分区消息实体][Partitioned messaging entities]。

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
