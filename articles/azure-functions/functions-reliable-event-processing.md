---
title: Azure Functions 可靠事件处理
description: 避免 Azure Functions 中缺少事件中心消息
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561861"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions 可靠事件处理

事件处理是与无服务器体系结构关联的最常见的方案之一。 本文介绍如何使用 Azure Functions 创建可靠消息处理器，以避免丢失消息。

## <a name="challenges-of-event-streams-in-distributed-systems"></a>分布式系统中的事件流的挑战

假设系统每秒发送一次事件的速率为每秒100事件。 在此时段内，多个并行函数实例每秒可以消耗传入的100事件。

但是，可以采用以下任何最佳条件：

- 如果事件发布者发送损坏的事件怎么办？
- 如果函数实例遇到未经处理的异常怎么办？
- 如果下游系统脱机，该怎么办？

如何处理这些情况，同时保留应用程序的吞吐量？

对于队列，可靠的消息传递自然就会发生。 与函数触发器配对时，函数会在队列消息上创建锁。 如果处理失败，则释放锁以允许另一个实例重试处理。 然后，处理将继续，直到成功评估消息或将消息添加到病毒队列。

即使单个队列消息可能仍在重试循环中，其他并行执行仍会继续保持取消排队剩余的消息。 结果是，整体吞吐量在很大程度上不受一条错误消息影响。 但是，存储队列不保证顺序，也不会针对事件中心所需的高吞吐量需求进行优化。

与此相反，Azure 事件中心不包含锁定概念。 为了允许高吞吐量、多个使用者组和重播功能等功能，事件中心事件的行为与视频播放器更类似。 每个分区从流中的单个点读取事件。 您可以从指针中向前或向后阅读该位置，但您必须选择移动要处理的事件的指针。

当流中出现错误时，如果您决定将指针保留在同一点，则事件处理将被阻止，直到指针高级为止。 换句话说，如果在处理单个事件时指针停止处理问题，则未处理的事件将开始堆积。

Azure Functions 通过在不成功或失败时将流的指针向前移动来避免死锁。 由于指针不断提高，因此您的函数需要正确处理故障。

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Azure Functions 如何使用事件中心事件

Azure Functions 使用事件中心事件，同时循环执行以下步骤：

1. 在 Azure 存储中为事件中心的每个分区创建并保存指针。
2. 接收到新消息（默认情况下为批）时，主机将尝试使用消息批触发函数。
3. 如果该函数完成执行（有或没有异常），指针将前移，并将检查点保存到存储帐户。
4. 如果条件阻止函数执行完成，则宿主无法进行指针。 如果指针不是高级的，稍后会检查处理相同的消息。
5. 重复步骤 2-4

此行为显示了几个要点：

- *未经处理的异常可能会导致丢失消息。* 导致异常的执行将继续进行指针。
- *函数可保证至少一次送达。* 你的代码和依赖系统可能需要[考虑是否可以接收两次同一消息](./functions-idempotent.md)。

## <a name="handling-exceptions"></a>处理异常

作为一般规则，每个函数应在代码的最高级别包含[try/catch 块](./functions-bindings-error-pages.md)。 具体而言，使用事件中心事件的所有函数都应有一个 `catch` 块。 这样一来，引发异常时，catch 块会在指针前进前处理错误。

### <a name="retry-mechanisms-and-policies"></a>重试机制和策略

某些异常在本质上是暂时性的，在稍后尝试再次尝试操作时不会重新出现。 这就是为什么第一步总是重试该操作的原因。 您可以自己编写重试处理规则，但很多工具都是可用的。 通过使用这些库，可以定义强健的重试策略，这也有助于保留处理顺序。

通过向函数引入错误处理库，可以定义基本和高级重试策略。 例如，你可以实现遵循以下规则所示的工作流的策略：

- 尝试插入一条消息三次（可能出现两次重试之间的延迟）。
- 如果所有重试操作的最终结果为失败，则将消息添加到队列，以便处理可以在流上继续。
- 稍后将处理损坏的或未处理的消息。

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly)是C#应用程序的复原和暂时性故障处理库的示例。

使用预编译C#的类库时，[异常筛选器](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch)使你可以在发生未经处理的异常时运行代码。

[AZURE WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)存储库中提供了演示如何使用异常筛选器的示例。

## <a name="non-exception-errors"></a>非异常错误

即使不存在错误，也会出现一些问题。 例如，请考虑执行过程中发生的失败。 在这种情况下，如果函数未完成执行，则不会执行偏移指针。 如果指针未前进，则在执行失败之后运行的任何实例将继续读取相同的消息。 这种情况提供 "至少一次" 保障。

确保每条消息至少被处理一次，这意味着某些消息可能会被处理多次。 函数应用需要了解这种可能性，并且必须围绕[幂等性的原则](./functions-idempotent.md)构建。

## <a name="stop-and-restart-execution"></a>停止并重新启动执行

虽然几个错误可能是可接受的，但如果您的应用程序遇到重大故障，该怎么办？ 你可能想要停止事件触发，直到系统达到正常状态。 使用断路器模式通常可以暂停处理。 断路器模式允许应用程序 "中断" 事件进程的线路，稍后再恢复。

在事件进程中实现断路器需要两个片段：

- 跨所有实例的共享状态，用于跟踪和监视线路的运行状况
- 可以管理线路状态（打开或关闭）的主进程

实现详细信息可能会有所不同，但要在实例之间共享状态，则需要存储机制。 你可以选择将状态存储在 Azure 存储、Redis 缓存或函数集合可以访问的任何其他帐户中。

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)或[持久实体](./durable/durable-functions-overview.md)适用于管理工作流和线路状态。 其他服务也可以正常工作，但在此示例中使用了逻辑应用。 使用逻辑应用时，可以暂停和重新启动函数的执行，为你提供实现断路器模式所需的控件。

### <a name="define-a-failure-threshold-across-instances"></a>在实例间定义失败阈值

若要为多个实例同时处理事件，需要保持共享外部状态以监视线路的运行状况。

你可以选择实现的规则可能会强制执行以下操作：

- 如果所有实例在30秒内的最终失败超过100，则中断线路，并在新消息上停止触发。

根据你的需求，实现详细信息会有所不同，但一般而言，你可以创建一个系统来执行以下操作：

1. 将失败记录到存储帐户（Azure 存储、Redis 等）
1. 记录新的故障时，检查滚动计数以查看是否满足阈值（例如，过去30秒内超过100）。
1. 如果满足阈值，则将事件发送到 Azure 事件网格，告诉系统中断线路。

### <a name="managing-circuit-state-with-azure-logic-apps"></a>通过 Azure 逻辑应用管理线路状态

以下说明介绍了一种创建 Azure 逻辑应用以使函数应用停止处理的方式。

Azure 逻辑应用随附了不同服务的内置连接器，具有有状态业务流程，并且是管理线路状态的一种自然选择。 检测到线路需要中断后，可以构建逻辑应用来实现以下工作流：

1. 触发事件网格工作流并停止 Azure 函数（使用 Azure 资源连接器）
1. 发送通知电子邮件，其中包含用于重新启动工作流的选项

电子邮件收件人可调查线路的运行状况，并在适当时通过通知电子邮件中的链接重新启动线路。 当工作流重新启动函数时，消息将从最后一个事件中心检查点进行处理。

使用此方法时，不会丢失任何消息，按顺序处理所有消息，并且可以根据需要中断线路。

## <a name="resources"></a>资源

- [可靠的事件处理示例](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure Durable Functions 断路器](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

- [Azure Functions 错误处理](./functions-bindings-error-pages.md)
- [使用事件网格自动调整已上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [创建与 Azure 逻辑应用集成的函数](./functions-twitter-email.md)
