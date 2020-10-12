---
title: Azure Functions 最佳做法
description: 了解 Azure Functions 的最佳做法和模式。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697684"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>优化 Azure Functions 的性能和可靠性

本文为提高[无服务器](https://azure.microsoft.com/solutions/serverless/)函数应用的性能和可靠性提供了指南。  

## <a name="general-best-practices"></a>常规最佳做法

下面是有关如何使用 Azure Functions 生成和构建无服务器解决方案的最佳做法。

### <a name="avoid-long-running-functions"></a>避免使用长时间运行的函数

长时间运行的大型函数可能会引起意外超时问题。 若要详细了解给定托管计划的超时，请参阅[函数应用超时持续时间](functions-scale.md#timeout)。 

由于含有许多 Node.js 依赖项，函数规模可能会变得很大。 导入依赖项也会导致加载时间增加，引起意外的超时问题。 显式和隐式加载依赖项。 由代码加载的单个模块可能会加载自己的附加模块。 

尽可能将大型函数重构为可协同工作且快速返回响应的较小函数集。 例如，webhook 或 HTTP 触发器函数可能需要在特定时间限制内确认响应；webhook 需要快速响应，这很常见。 可将 HTTP 触发器有效负载传递到由队列触发器函数处理的队列。 此方法允许延迟实际工作并返回即时响应。


### <a name="cross-function-communication"></a>跨函数通信

[Durable Functions](durable/durable-functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)用于管理状态转换以及多个函数之间的通信。

如果不使用 Durable Functions 或逻辑应用来集成多个函数，则最好是将存储队列用于跨函数通信。 主要原因是与其他存储选项相比，存储队列成本更低且更易预配。 

存储队列中各消息的大小限制为 64 KB。 如果需要在函数之间传递更大的消息，可使用 Azure 服务总线队列，以在标准层中支持最大为 256 KB 的消息大小，在高级层中最大为 1 MB 的消息大小。

如果在处理前需要筛选消息，则服务总线主题十分有用。

对于支持大容量通信，事件中心十分有用。


### <a name="write-functions-to-be-stateless"></a>将函数编写为无状态 

如有可能，函数应为无状态和幂等。 将任何所需的状态信息与用户的数据相关联。 例如，正在处理的排序可能具有关联的 `state` 成员。 函数本身保持无状态时，该函数可根据该状态处理排序。 

对于计时器触发器，特别建议采用幂等函数。 例如，如果有必须每天运行一次的内容，则编写它，使它可在一天内的任何时间运行，并生成相同的结果。 某天没有任何工作时，可退出该函数。 此外，如果未能完成以前的运行，则下次运行应从中断的位置继续运行。


### <a name="write-defensive-functions"></a>编写防御函数

假定任何时候函数都可能会遇到异常。 设计函数，使其具有在下次执行期间从上一失败点继续执行的能力。 请考虑需执行以下操作的方案：

1. 在数据库中进行 10,000 行的查询。
2. 为每行创建队列消息，从而处理下一行。
 
根据系统复杂程度，可能有：行为有误的相关下游服务，网络故障或已达配额限制等等。所有这些可在任何时间影响用户的函数。 需设计函数，使其做好该准备。

如果将 5,000 个那些项插入到队列中进行处理，然后发生故障，代码将如何响应？ 跟踪已完成的一组中的项。 否则，下次可能再次插入它们。 这种双插入可能会严重影响工作流，因此请[将函数设置为幂等](functions-idempotent.md)。 

如果已处理队列项，则允许函数不执行任何操作。

利用已为 Azure Functions 平台中使用的组件提供的防御措施。 有关示例，请参阅 [Azure 存储队列触发器和绑定](functions-bindings-storage-queue-trigger.md#poison-messages)文档中的**处理有害队列消息**。 

## <a name="scalability-best-practices"></a>可伸缩性最佳做法

有许多因素会影响函数应用实例的缩放方式。 有关[函数缩放](functions-scale.md)的文档中提供了详细信息。  下面是确保以最佳方式缩放函数应用的最佳做法。

### <a name="share-and-manage-connections"></a>共享和管理连接

只要可能，请重用与外部资源的连接。 请参阅[如何管理 Azure Functions 中的连接](./manage-connections.md)。

### <a name="avoid-sharing-storage-accounts"></a>避免共享存储帐户

创建函数应用时，必须将其与存储帐户相关联。 存储帐户连接在 [AzureWebJobsStorage 应用程序设置](./functions-app-settings.md#azurewebjobsstorage)中维护。 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>请勿在同一函数应用中混合测试和生产代码

Function App 中的各函数共享资源。 例如，共享内存。 如果生产中使用的是 Function App，则请勿向其添加与测试相关的函数和资源。 生产代码执行期间，这可能会导致意外的开销。

请注意在生产 Function App 中加载的内容。 将内存平均分配给应用中的每个函数。

如果在多个 .NET 函数中引用共享程序集，请将其放在常用的共享文件夹中。 否则，可能会意外部署在函数之间表现不同的同一二进制的多个版本。

请勿在生产代码中使用详细日志记录，否则会对性能产生负面影响。

### <a name="use-async-code-but-avoid-blocking-calls"></a>使用异步代码，但避免阻止调用

异步编程是推荐的最佳做法，在涉及到阻止 I/O 操作时更是如此。

在 C# 中，请始终避免引用 `Result` 属性或在 `Task` 实例上调用 `Wait` 方法。 这种方法会导致线程耗尽。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>使用多个工作进程

默认情况下，Functions 的任何主机实例均使用单个工作进程。 若要提高性能，尤其是在单线程运行时（如 Python），请使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 将每个主机的工作进程数增加 (多达10个) 。 然后，Azure Functions 会尝试在这些工作进程之间平均分配同步函数调用。 

FUNCTIONS_WORKER_PROCESS_COUNT 适用于 Functions 在横向扩展应用程序以满足需求时创建的每个主机。 

### <a name="receive-messages-in-batch-whenever-possible"></a>尽量批量接收消息

某些触发器（例如事件中心）允许通过单次调用接收一批消息。  批处理消息可大幅提升性能。  可以根据 [host.json 参考文档](functions-host-json.md)中的详述，在 `host.json` 文件中配置最大批大小

对于 C# 函数，可将类型更改为强类型化数组。  例如，方法签名可以是 `EventData[] sensorEvent`，而不是 `EventData sensorEvent`。  对于其他语言，需要根据[此文所述](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)，在 `function.json` 中将基数属性显式设置为 `many`，以启用批处理。

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>配置主机行为以更好地处理并发性

使用函数应用中的 `host.json` 文件可以配置主机运行时和触发器行为。  除了批处理行为以外，还可以管理大量触发器的并发性。 调整这些选项中的值往往有助于每个实例根据被调用函数的需求适当缩放。

host.json 文件中的设置应用于应用中的所有函数，以及函数的单个实例。  例如，如果有包含两个 HTTP 函数的函数应用，并且 [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) 请求设置为 25，则针对任一 HTTP 触发器发出的请求将计入 25 个共享的并发请求。  如果该函数应用扩展到 10 个实例，则两个函数会有效地允许 250 个并发请求（10 个实例 * 每个实例 25 个并发请求）。 

可在 [host.json 配置文章](functions-host-json.md)在找到其他主机配置选项。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [如何在 Azure Functions 中管理连接](manage-connections.md)
* [Azure 应用服务最佳实践](../app-service/app-service-best-practices.md)
