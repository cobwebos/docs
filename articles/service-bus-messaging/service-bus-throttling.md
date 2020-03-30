---
title: Azure 服务总线限制概述 |微软文档
description: 服务总线限制概述 - 标准和高级层。
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598283"
---
# <a name="throttling-operations-on-azure-service-bus"></a>在 Azure 服务总线上限制操作

云原生解决方案提供了可随工作负载扩展的无限资源的概念。 虽然这种概念在云中比使用本地系统更真实，但云中仍然存在一些限制。

这些限制可能会导致标准层和高级层中的客户端应用程序请求限制，如本文所述。 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure 服务总线标准层中的限制

Azure 服务总线标准层作为多租户设置运行，具有即用即付定价模型。 在这里，同一群集中的多个命名空间共享分配的资源。 标准层是开发人员、测试和 QA 环境以及低吞吐量生产系统的首选。

过去，Azure 服务总线具有严格依赖于资源利用率的粗限制限制。 但是，有机会优化限制逻辑，并将可预测的限制行为提供给共享这些资源的所有命名空间。

为了确保资源在使用相同资源的所有 Azure 服务总线标准命名空间中合理使用和分配，限制逻辑已修改为基于信用。

> [!NOTE]
> 请务必注意，限制对于 Azure 服务总线或任何云本机服务***来说并不新鲜***。
>
> 基于信用的限制只是改进各种命名空间在多租户标准层环境中共享资源的方式，从而实现共享资源的所有命名空间的公平使用。

### <a name="what-is-credit-based-throttling"></a>什么是基于信用的限制？

基于信用的限制限制限制在特定时间段内可以在给定命名空间上执行的操作数。 

下面是基于信用的限制工作流 - 

  * 在每个时间段的开始，我们为每个命名空间提供一定数量的配额。
  * 发送方或接收方客户端应用程序执行的任何操作都将计入这些积分（并从可用积分中减去）。
  * 如果积分耗尽，后续操作将限制到下一个时间段的开始。
  * 积分在下一个时间段开始时补充。

### <a name="what-are-the-credit-limits"></a>信用额度是多少？

信用额度当前设置为每秒"1000"学分（每个命名空间）。

并非所有操作都创建相等。 以下是每个操作的信用成本 - 

| Operation | 信贷成本|
|-----------|-----------|
| 数据操作（发送、发送同步、接收、接收同步、透视） |每条消息 1 个积分 |
| 管理操作（创建、读取、更新、删除队列、主题、订阅、筛选器） | 10 学分 |

> [!NOTE]
> 请注意，在发送到主题时，在订阅上提供之前，会根据筛选器对每条消息进行评估。
> 每个滤波器评估还计入信用额度（即每个筛选器评估 1 个贷项）。
>

### <a name="how-will-i-know-that-im-being-throttled"></a>我怎么知道我被限制了？

当客户端应用程序请求被限制时，应用程序将接收并记录以下服务器响应。

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>如何避免被限制？

使用共享资源时，在共享这些资源的各种服务总线命名空间中强制实施某种公平使用非常重要。 限制可确保单个工作负荷中的任何峰值不会导致同一资源上的其他工作负载受到限制。

如本文后面所述，由于客户端 SDK（和其他 Azure PaaS 产品）内置了默认重试策略，因此没有被限制的风险。 任何节流请求都将使用指数回退重试，并最终在补充积分时完成。

可以理解的是，某些应用程序可能对被限制敏感。 在这种情况下，建议将[当前服务总线标准命名空间迁移到高级](service-bus-migrate-standard-premium.md)。 

在迁移时，您可以将专用资源分配给服务总线命名空间，并在工作负荷出现峰值时适当扩展资源，并减少被限制的可能性。 此外，当工作负荷减少到正常级别时，可以缩减分配给命名空间的资源。

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure 服务总线高级层中的限制

[Azure 服务总线高级](service-bus-premium-messaging.md)层将专用资源（按消息传递单位）分配给客户设置的每个命名空间。 这些专用资源提供可预测的吞吐量和延迟，建议用于高吞吐量或敏感生产级系统。

此外，高级层还使客户在遇到工作负载高峰时能够扩展其吞吐量容量。

### <a name="how-does-throttling-work-in-service-bus-premium"></a>在服务总线高级版中限制如何工作？

使用服务总线高级版的独家资源分配时，限制纯粹是受分配给命名空间的资源的限制驱动的。

如果请求数超过当前资源可以提供的服务，则将限制请求。

### <a name="how-will-i-know-that-im-being-throttled"></a>我怎么知道我被限制了？

在 Azure 服务总线高级版中识别限制的方法有多种 - 
  * **受限请求**显示在[Azure 监视器请求指标](service-bus-metrics-azure-monitor.md#request-metrics)上，以标识有多少请求被限制。
  * **CPU 使用率**高表示当前资源分配较高，如果当前工作负载不减少，请求可能会受到限制。
  * **高内存使用**率表示当前资源分配较高，如果当前工作负载不减少，请求可能会受到限制。

### <a name="how-can-i-avoid-being-throttled"></a>如何避免被限制？

由于服务总线高级命名空间已具有专用资源，因此可以通过在工作负载高峰事件（或预期）中扩展分配给命名空间的消息单元数来降低被限制的可能性。

通过创建可由上述指标中的更改触发[的 Runbook，](../automation/automation-create-alert-triggered-runbook.md)可以向上/向下扩展。

## <a name="faqs"></a>常见问题解答

### <a name="how-does-throttling-affect-my-application"></a>限制如何影响我的应用程序？

当请求被限制时，这意味着服务处于繁忙，因为它面对的请求比资源允许的请求多。 如果几分钟后再次尝试相同的操作，则一旦服务完成其当前工作负载，则可以遵守请求。

由于限制是任何云本机服务的预期行为，因此我们已将重试逻辑构建到 Azure 服务总线 SDK 本身中。 默认值设置为使用指数回退自动重试，以确保我们每次没有使用相同的请求被限制。

默认重试逻辑将应用于每个操作。

### <a name="does-throttling-result-in-data-loss"></a>限制是否导致数据丢失？

Azure 服务总线针对持久性进行了优化，我们确保发送到服务总线的所有数据在服务确认请求成功之前都承诺存储。

一旦服务总线成功"确认"（确认）请求，则意味着服务总线已成功处理该请求。 如果服务总线返回"NACK"（失败），则意味着服务总线无法处理请求，并且客户端应用程序必须重试请求。

但是，当请求受到限制时，服务意味着由于资源限制，它现在无法接受和处理请求。 **这并不意味着**任何类型的数据丢失，因为服务总线根本没有查看请求。 在这种情况下，依赖服务总线 SDK 的默认重试策略可确保最终处理请求。

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息概述](service-bus-messaging-overview.md)
* [快速入门：使用 Azure 门户和 .NET 发送和接收消息](service-bus-quickstart-portal.md)
* [教程：使用 Azure 门户和主题/订阅更新清单](service-bus-tutorial-topics-subscriptions-portal.md)

