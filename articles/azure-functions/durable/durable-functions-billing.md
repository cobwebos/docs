---
title: Durable Functions 计费 - Azure Functions
description: 了解 Durable Functions 的内部行为，及其对 Azure Functions 计费的影响。
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "74233013"
---
# <a name="durable-functions-billing"></a>Durable Functions 计费

[Durable Functions](durable-functions-overview.md) 的计费方式与 Azure Functions 相同。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。

在 Azure Functions [消耗计划](../functions-scale.md#consumption-plan)中执行业务流程协调程序函数时，需要注意一些计费行为。 以下部分更详细地介绍了这些行为及其影响。

## <a name="orchestrator-function-replay-billing"></a>业务流程协调程序函数重播计费

[业务流程协调程序函数](durable-functions-orchestrations.md)在业务流程的整个生存期内可能会重播多次。 Azure Functions 运行时将每次重播视为不同的函数调用。 出于此原因，在 Azure Functions 消耗计划中，每次重播业务流程协调程序函数都会产生费用。 其他计划类型不会对业务流程协调程序函数重播收费。

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>业务流程协调程序函数中的等待和生成

当业务流程协调程序函数使用 **await** (C#) 或 **yield** (JavaScript) 等待某个异步操作完成时，运行时会将该特定执行视为已完成。 业务流程协调程序函数的计费此时会停止。 在下一次业务流程协调程序函数重播之前，计费不会恢复。 你不需要对业务流程协调程序函数中的等待或生成所花费的任何时间付费。

> [!NOTE]
> 某些运行时将调用其他函数的函数视为反模式。 原因是存在一个所谓“双重计费”的问题。  当某个函数直接调用另一个函数时，两者会同时运行。 被调用函数正在实际运行代码，而调用函数正在等待响应。 在这种情况下，必须为调用函数等待被调用函数运行所花费的时间付费。
>
> 业务流程协调程序函数不会双重计费。 业务流程协调程序函数在等待活动函数或子业务流程的结果时计费将会停止。

## <a name="durable-http-polling"></a>持久 HTTP 轮询

如 [HTTP 功能](durable-functions-http-features.md)一文中所述，业务流程协调程序函数可对外部终结点发出长时间运行的 HTTP 调用。 在遵循[异步 202 模式](durable-functions-http-features.md#http-202-handling)时，**CallHttpAsync** 方法 (C#) 和 **callHttp** 方法 (JavaScript) 可在内部轮询 HTTP 终结点。

内部 HTTP 轮询操作目前不会产生直接费用。 但是，内部轮询可能导致业务流程协调程序函数定期重播。 你要支付这些内部函数重播的标准费用。

## <a name="azure-storage-transactions"></a>Azure 存储事务

Durable Functions 默认使用 Azure 存储通过 Blob 租约来持久保存状态、处理消息和管理分区。 此存储帐户由你拥有，因此，任何事务成本都从你的 Azure 订阅计收。 有关 Durable Functions 使用的 Azure 存储项目的详细信息，请参阅[任务中心](durable-functions-task-hubs.md)一文。

多种因素会影响 Durable Functions 应用产生的实际 Azure 存储成本：

* 单个函数应用与单个任务中心关联，后者共享一组 Azure 存储资源。 这些资源由函数应用中的所有 Durable Functions 使用。 函数应用中的实际函数数目不会影响 Azure 存储事务成本。
* 每个函数应用实例在内部使用指数退避轮询算法轮询存储帐户中的多个队列。 空闲应用实例的轮询队列频率低于活跃的应用，因此事务成本更低。 有关 Durable Functions 队列轮询行为的详细信息，请参阅[“性能和缩放”一文的“队列轮询”部分](durable-functions-perf-and-scale.md#queue-polling)。
* 在 Azure Functions 消耗计划或高级计划中运行时，[Azure Functions 规模控制器](../functions-scale.md#how-the-consumption-and-premium-plans-work)将在后台定期轮询所有任务中心队列。 在中小规模的函数应用中，只有一个规模控制器实例轮询这些队列。 如果函数应用横向扩展到大量实例，可以添加更多的规模控制器实例。 这些附加的规模控制器实例可能会增大队列事务总成本。
* 每个函数应用实例争用一组 Blob 租约。 这些实例将定期调用 Azure Blob 服务，以续订持有的租约，或尝试获取新租约。 任务中心的已配置分区计数决定了 Blob 租约数。 横向扩展到更多函数应用实例可能会增大与这些租约操作相关的 Azure 存储事务成本。

可以在 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)文档中找到有关 Azure 存储定价的详细信息。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)
