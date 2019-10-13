---
title: Azure 服务总线限制概述 |Microsoft Docs
description: Service Bus 限制概述-标准层和高级层。
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: 21a3bfd09e83571e489e15e9351e12220a99e563
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301251"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Azure 服务总线上的限制操作

云本机解决方案提供了一种无限制资源的概念，可根据工作负载进行缩放。 虽然这种概念在云中比在本地系统中更加真实，但云中仍然存在一些限制。

这些限制可能会导致在标准层和高级层中阻止客户端应用程序请求，如本文所述。 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure Service Bus 标准层中的限制

Azure 服务总线标准级别作为使用即用即付定价模型的多租户设置运行。 此处，同一群集中的多个命名空间共享分配的资源。 标准层是适用于开发人员、测试和 QA 环境的建议选择，同时还提供低吞吐量的生产系统。

过去，Azure 服务总线严格依赖于资源利用率。 不过，有机会改进限制逻辑，并为共享这些资源的所有命名空间提供可预测的限制行为。

为了确保在使用相同资源的所有 Azure 服务总线标准命名空间中公平地使用资源并分配资源，阻止逻辑已修改为基于信用额度。

> [!NOTE]
> 需要特别注意的是，限制并***不***是 Azure 服务总线或任何云本机服务的新功能。
>
> 基于信用额度的限制只是细化了各种命名空间在多租户标准层环境中共享资源的方式，从而为共享资源的所有命名空间启用了公平使用。

### <a name="what-is-credit-based-throttling"></a>什么是基于信用的限制？

基于信用的限制可以限制特定时间段内给定命名空间上可以执行的操作的数目。 

下面是基于信用限制的工作流 

  * 在每个时间段开始时，我们将为每个命名空间提供一定数量的信用额度。
  * 发件人或接收方客户端应用程序执行的任何操作都将按这些信用额度计算（并从可用信用额度中扣除）。
  * 如果信用额度用尽，后续操作将被限制到下一个时间段的开始时间。
  * 信用额度将在下一个时间段的开始时间补充。

### <a name="what-are-the-credit-limits"></a>信用限制是多少？

信用额度当前设置为每秒（每个命名空间）的 "1000" 信用额度。

并非所有操作都是相等的。 下面是每个操作的信用成本- 

| 操作 | 信用成本|
|-----------|-----------|
| 数据操作（发送、SendAsync、接收、ReceiveAsync、速览） |每条消息1个信用 |
| 管理操作（创建、读取、更新、删除队列、主题、订阅、筛选器） | 10个信用 |

### <a name="how-will-i-know-that-im-being-throttled"></a>我如何知道我会受到限制？

当客户端应用程序请求受到限制时，应用程序将接收以下服务器响应并进行记录。

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>如何避免被阻止？

使用共享资源，在共享这些资源的各种服务总线命名空间之间强制实施某种公平使用非常重要。 限制确保单个工作负荷中的任何峰值不会导致相同资源上的其他工作负荷受到限制。

如本文后面所述，由于客户端 Sdk （以及其他 Azure PaaS 产品/服务）具有内置的默认重试策略，因此不会受到限制。 将使用指数回退重试任何受限请求，并最终会在信用额度补充时进行。

理解，某些应用程序可能会受到限制。 在这种情况下，建议将[当前的 Service Bus 标准命名空间迁移到高级命名空间](service-bus-migrate-standard-premium.md)。 

在迁移时，可以将专用资源分配到服务总线命名空间，并在工作负荷中出现峰值时适当地扩展资源，并减少受到限制的可能性。 此外，当工作负荷降到正常级别时，可以缩减分配给命名空间的资源。

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure 服务总线高级层中的限制

[Azure 服务总线高级](service-bus-premium-messaging.md)层根据消息传送单元向客户设置的每个命名空间分配专用资源。 这些专用资源可提供可预测的吞吐量和延迟，建议用于高吞吐量或敏感的生产级系统。

此外，高级层还可让客户在工作负荷高峰时增加其吞吐量容量。

### <a name="how-does-throttling-work-in-service-bus-premium"></a>服务总线高级版中的限制是如何工作的？

对于服务总线高级版的独占资源分配，限制仅由分配给命名空间的资源的限制驱动。

如果请求数超过当前资源可以服务，则会限制请求。

### <a name="how-will-i-know-that-im-being-throttled"></a>我如何知道我会受到限制？

可以通过多种方式在 Azure 服务总线高级版中标识限制- 
  * 受**限制的请求**显示在[Azure Monitor 请求度量值](service-bus-metrics-azure-monitor.md#request-metrics)上，以确定限制了多少请求。
  * **CPU 使用率**高表示当前资源分配较高，如果当前工作负荷不会减少，请求可能会受到限制。
  * "高**内存使用率**" 指示当前资源分配较高，如果当前工作负荷不减少，请求可能会受到限制。

### <a name="how-can-i-avoid-being-throttled"></a>如何避免被阻止？

由于服务总线高级命名空间已具有专用资源，因此可以通过在工作负荷中的峰值的事件（或预期）中增加分配给命名空间的消息传送单元数来减少受到限制的可能性。

可以通过创建可通过上述指标的更改触发的[runbook](../automation/automation-create-alert-triggered-runbook.md)来实现向上/向下扩展。

## <a name="faqs"></a>常见问题

### <a name="how-does-throttling-affect-my-application"></a>限制如何影响应用程序？

当某个请求受到限制时，它表示该服务正忙，因为它的请求数超过了资源允许的数量。 如果在一段时间后重试相同的操作，则在该服务完成其当前工作负荷后，就可以接受该请求。

由于限制是任何云本机服务的预期行为，因此我们已将重试逻辑内置于 Azure 服务总线 SDK 本身。 默认设置为 "使用指数回退自动重试"，以确保每次都不会限制相同的请求。

默认的重试逻辑将应用于每个操作。

### <a name="does-throttling-result-in-data-loss"></a>限制是否会导致数据丢失？

Azure 服务总线针对持久性进行了优化，我们确保发送到服务总线的所有数据在服务确认请求成功之前都提交给存储。

通过服务总线成功地 "确认" （确认）后，它表示服务总线已成功处理请求。 如果服务总线返回了 "NACK" （失败），则表示服务总线无法处理该请求，客户端应用程序必须重试该请求。

但是，当请求受到限制时，服务表示它现在无法接受并处理请求，因为存在资源限制。 这**并不**意味着任何数据丢失，因为服务总线只是未查看请求。 在这种情况下，依赖服务总线 SDK 的默认重试策略可确保最终处理请求。

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [快速入门：使用 Azure 门户和 .NET 发送和接收消息](service-bus-quickstart-portal.md)
* [教程：使用 Azure 门户和主题/订阅更新清单](service-bus-tutorial-topics-subscriptions-portal.md)

