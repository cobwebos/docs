---
title: Azure Functions 最佳做法
description: 了解 Azure Functions 的最佳做法和模式。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 10/16/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa85f636233a067713d127938d674b359bd03696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227369"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>优化 Azure Functions 的性能和可靠性

本文为提高[无服务器](https://azure.microsoft.com/solutions/serverless/)函数应用的性能和可靠性提供了指南。  

## <a name="general-best-practices"></a>常规最佳做法

下面是有关如何使用 Azure Functions 生成和构建无服务器解决方案的最佳做法。

### <a name="avoid-long-running-functions"></a>避免使用长时间运行的函数

长时间运行的大型函数可能会引起意外超时问题。 To learn more about the timeouts for a given hosting plan, see [function app timeout duration](functions-scale.md#timeout). 

A function can become large because of many Node.js dependencies. 导入依赖项也会导致加载时间增加，引起意外的超时问题。 显式和隐式加载依赖项。 由代码加载的单个模块可能会加载自己的附加模块。 

尽可能将大型函数重构为可协同工作且快速返回响应的较小函数集。 For example, a webhook or HTTP trigger function might require an acknowledgment response within a certain time limit; it's common for webhooks to require an immediate response. 可将 HTTP 触发器有效负载传递到由队列触发器函数处理的队列。 This approach lets you defer the actual work and return an immediate response.


### <a name="cross-function-communication"></a>跨函数通信

[Durable Functions](durable/durable-functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)用于管理状态转换以及多个函数之间的通信。

If not using Durable Functions or Logic Apps to integrate with multiple functions, it's best to use storage queues for cross-function communication. The main reason is that storage queues are cheaper and much easier to provision than other storage options. 

存储队列中各消息的大小限制为 64 KB。 如果需要在函数之间传递更大的消息，可使用 Azure 服务总线队列，以在标准层中支持最大为 256 KB 的消息大小，在高级层中最大为 1 MB 的消息大小。

如果在处理前需要筛选消息，则服务总线主题十分有用。

对于支持大容量通信，事件中心十分有用。


### <a name="write-functions-to-be-stateless"></a>将函数编写为无状态 

如有可能，函数应为无状态和幂等。 将任何所需的状态信息与用户的数据相关联。 例如，正在处理的排序可能具有关联的 `state` 成员。 函数本身保持无状态时，该函数可根据该状态处理排序。 

对于计时器触发器，特别建议采用幂等函数。 For example, if you have something that absolutely must run once a day, write it so it can run anytime during the day with the same results. The function can exit when there's no work for a particular day. 此外，如果未能完成以前的运行，则下次运行应从中断的位置继续运行。


### <a name="write-defensive-functions"></a>编写防御函数

假定任何时候函数都可能会遇到异常。 设计函数，使其具有在下次执行期间从上一失败点继续执行的能力。 请考虑需执行以下操作的方案：

1. Query for 10,000 rows in a database.
2. 为每行创建队列消息，从而处理下一行。
 
Depending on how complex your system is, you may have: involved downstream services behaving badly, networking outages, or quota limits reached, etc. All of these can affect your function at any time. 需设计函数，使其做好该准备。

如果将 5,000 个那些项插入到队列中进行处理，然后发生故障，代码将如何响应？ 跟踪已完成的一组中的项。 否则，下次可能再次插入它们。 This double-insertion can have a serious impact on your work flow, so [make your functions idempotent](functions-idempotent.md). 

如果已处理队列项，则允许函数不执行任何操作。

利用已为 Azure Functions 平台中使用的组件提供的防御措施。 有关示例，请参阅 [Azure 存储队列触发器和绑定](functions-bindings-storage-queue.md#trigger---poison-messages)文档中的**处理有害队列消息**。 

## <a name="scalability-best-practices"></a>可伸缩性最佳做法

There are a number of factors that impact how instances of your function app scale. 有关[函数缩放](functions-scale.md)的文档中提供了详细信息。  下面是确保以最佳方式缩放函数应用的最佳做法。

### <a name="share-and-manage-connections"></a>共享和管理连接

Reuse connections to external resources whenever possible.  请参阅[如何管理 Azure Functions 中的连接](./manage-connections.md)。

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>请勿在同一函数应用中混合测试和生产代码

Function App 中的各函数共享资源。 例如，共享内存。 如果生产中使用的是 Function App，则请勿向其添加与测试相关的函数和资源。 生产代码执行期间，这可能会导致意外的开销。

请注意在生产 Function App 中加载的内容。 将内存平均分配给应用中的每个函数。

If you have a shared assembly referenced in multiple .NET functions, put it in a common shared folder. Otherwise, you could accidentally deploy multiple versions of the same binary that behave differently between functions.

Don't use verbose logging in production code, which has a negative performance impact.

### <a name="use-async-code-but-avoid-blocking-calls"></a>使用异步代码，但避免阻止调用

异步编程是推荐的最佳做法。 但是，请始终避免引用 `Result` 属性或在 `Task` 实例上调用 `Wait` 方法。 这种方法会导致线程耗尽。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>尽量批量接收消息

某些触发器（例如事件中心）允许通过单次调用接收一批消息。  批处理消息可大幅提升性能。  可以根据 [host.json 参考文档](functions-host-json.md)中的详述，在 `host.json` 文件中配置最大批大小

For C# functions, you can change the type to a strongly-typed array.  例如，方法签名可以是 `EventData[] sensorEvent`，而不是 `EventData sensorEvent`。  For other languages, you'll need to explicitly set the cardinality property in your `function.json` to `many` in order to enable batching [as shown here](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>配置主机行为以更好地处理并发性

使用函数应用中的 `host.json` 文件可以配置主机运行时和触发器行为。  除了批处理行为以外，还可以管理大量触发器的并发性。 调整这些选项中的值往往有助于每个实例根据被调用函数的需求适当缩放。

Settings in the host.json file apply across all functions within the app, within a *single instance* of the function. For example, if you had a function app with two HTTP functions and [`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings) requests set to 25, a request to either HTTP trigger would count towards the shared 25 concurrent requests.  When that function app is scaled to 10 instances, the two functions effectively allow 250 concurrent requests (10 instances * 25 concurrent requests per instance). 

Other host configuration options are found in the [host.json configuration article](functions-host-json.md).

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

* [如何在 Azure Functions 中管理连接](manage-connections.md)
* [Azure 应用服务最佳实践](../app-service/app-service-best-practices.md)
