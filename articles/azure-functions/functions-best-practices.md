---
title: Azure Functions 最佳做法 | Microsoft Docs
description: 了解 Azure Functions 的最佳做法和模式。
author: ggailey777
manager: gwallace
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87071b8e1102067110baae70c424aa74a5e0702c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73570829"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>优化 Azure Functions 的性能和可靠性

本文为提高[无服务器](https://azure.microsoft.com/solutions/serverless/)函数应用的性能和可靠性提供了指南。  

## <a name="general-best-practices"></a>常规最佳做法

下面是有关如何使用 Azure Functions 生成和构建无服务器解决方案的最佳做法。

### <a name="avoid-long-running-functions"></a>避免使用长时间运行的函数

长时间运行的大型函数可能会引起意外超时问题。 若要了解有关给定宿主计划的超时的详细信息，请参阅[函数应用超时持续时间](functions-scale.md#timeout)。 

由于许多 node.js 依赖项，函数可能会变得很大。 导入依赖项也会导致加载时间增加，引起意外的超时问题。 显式和隐式加载依赖项。 由代码加载的单个模块可能会加载自己的附加模块。 

尽可能将大型函数重构为可协同工作且快速返回响应的较小函数集。 例如，webhook 或 HTTP 触发器函数可能需要在特定时间限制内确认响应;webhook 通常需要立即响应。 可将 HTTP 触发器有效负载传递到由队列触发器函数处理的队列。 利用此方法，您可以延迟实际工作并返回立即响应。


### <a name="cross-function-communication"></a>跨函数通信

[Durable Functions](durable/durable-functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)用于管理状态转换以及多个函数之间的通信。

如果不使用 Durable Functions 或逻辑应用来与多个功能集成，最好将存储队列用于跨函数通信。 主要原因是，存储队列更便宜，预配起来比其他存储选项要容易得多。 

存储队列中各消息的大小限制为 64 KB。 如果需要在函数之间传递更大的消息，可使用 Azure 服务总线队列，以在标准层中支持最大为 256 KB 的消息大小，在高级层中最大为 1 MB 的消息大小。

如果在处理前需要筛选消息，则服务总线主题十分有用。

对于支持大容量通信，事件中心十分有用。


### <a name="write-functions-to-be-stateless"></a>将函数编写为无状态 

如有可能，函数应为无状态和幂等。 将任何所需的状态信息与用户的数据相关联。 例如，正在处理的排序可能具有关联的 `state` 成员。 函数本身保持无状态时，该函数可根据该状态处理排序。 

对于计时器触发器，特别建议采用幂等函数。 例如，如果你有一定一天必须运行一次的内容，请编写它，以便它可以在一天中的任何时间运行，但结果相同。 如果某一天没有工作，该函数可能会退出。 此外，如果未能完成以前的运行，则下次运行应从中断的位置继续运行。


### <a name="write-defensive-functions"></a>编写防御函数

假定任何时候函数都可能会遇到异常。 设计函数，使其具有在下次执行期间从上一失败点继续执行的能力。 请考虑需执行以下操作的方案：

1. 查询数据库中的10000行。
2. 为每行创建队列消息，从而处理下一行。
 
根据系统的复杂程度，你可能会遇到以下情况：所涉及的下游服务的行为错误、网络中断或达到了配额限制等。所有这些都可能会影响你的函数。 需设计函数，使其做好该准备。

如果将 5,000 个那些项插入到队列中进行处理，并发生故障，代码将如何响应？ 跟踪已完成的一组中的项。 否则，下次可能再次插入它们。 这种双插入操作可能会对工作流产生严重影响，因此[使函数成为幂等](functions-idempotent.md)的。 

如果已处理队列项，则允许函数不执行任何操作。

利用已为 Azure Functions 平台中使用的组件提供的防御措施。 有关示例，请参阅 **Azure 存储队列触发器和绑定**文档中的[处理有害队列消息](functions-bindings-storage-queue.md#trigger---poison-messages)。 

## <a name="scalability-best-practices"></a>可伸缩性最佳做法

有许多因素会影响函数应用程序的实例的缩放方式。 有关[函数缩放](functions-scale.md)的文档中提供了详细信息。  下面是确保以最佳方式缩放函数应用的最佳做法。

### <a name="share-and-manage-connections"></a>共享和管理连接

尽可能重复使用与外部资源的连接。  请参阅[如何管理 Azure Functions 中的连接](./manage-connections.md)。

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>请勿在同一函数应用中混合测试和生产代码

Function App 中的各函数共享资源。 例如，共享内存。 如果生产中使用的是 Function App，则请勿向其添加与测试相关的函数和资源。 生产代码执行期间，这可能会导致意外的开销。

请注意在生产 Function App 中加载的内容。 将内存平均分配给应用中的每个函数。

如果在多个 .NET 函数中引用共享程序集，请将其放在常用的共享文件夹中。 否则，可能会意外地部署不同函数之间行为不同的多个版本。

不要在生产代码中使用详细日志记录，这会对性能产生负面影响。

### <a name="use-async-code-but-avoid-blocking-calls"></a>使用异步代码，但避免阻止调用

异步编程是推荐的最佳做法。 但是，请始终避免引用 `Result` 属性或在 `Wait` 实例上调用 `Task` 方法。 这种方法会导致线程耗尽。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>尽量批量接收消息

某些触发器（例如事件中心）允许通过单次调用接收一批消息。  批处理消息可大幅提升性能。  可以根据 `host.json`host.json 参考文档[中的详述，在 ](functions-host-json.md) 文件中配置最大批大小

对于C#函数，可以将类型更改为强类型数组。  例如，方法签名可以是 `EventData sensorEvent`，而不是 `EventData[] sensorEvent`。  对于其他语言，你需要将 `function.json` 中的基数属性显式设置为 `many` 以启用批处理，[如下所示](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)。

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>配置主机行为以更好地处理并发性

使用函数应用中的 `host.json` 文件可以配置主机运行时和触发器行为。  除了批处理行为以外，还可以管理大量触发器的并发性。 调整这些选项中的值往往有助于每个实例根据被调用函数的需求适当缩放。

Host json 文件中的设置适用于应用中的所有函数，该函数的*单个实例*内。 例如，如果你有一个具有两个 HTTP 函数的 function app，并且[`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings)的请求设置为25，则对 HTTP 触发器的请求将计入共享的25个并发请求。  当该函数应用扩展为10个实例时，两个函数有效地允许250个并发请求（每个实例有10个实例 * 25 个并发请求）。 

其他主机配置选项可在 "[主机" 配置一文](functions-host-json.md)中找到。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [如何在 Azure Functions 中管理连接](manage-connections.md)
* [Azure 应用服务最佳实践](../app-service/app-service-best-practices.md)
