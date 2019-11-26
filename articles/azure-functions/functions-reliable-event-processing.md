---
title: Azure Functions 可靠事件处理
description: 避免 Azure Functions 中缺少事件中心消息
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: 019c44cedba166dc1ac06a0244fa2b2e7930e673
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230376"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions 可靠事件处理

事件处理是与无服务器体系结构关联的最常见场景之一。 本文介绍如何使用 Azure Functions 创建可靠的消息处理器，以避免丢失消息。

## <a name="challenges-of-event-streams-in-distributed-systems"></a>分布式系统中的事件流的难题

假设某个系统以每秒 100 个事件的恒定速率发送事件。 根据这种速率，在数分钟内，就有多个并行 Functions 实例可以每秒使用 100 个传入事件。

但是，也可能会出现以下任何欠佳的情况：

- 如果事件发布者发送了损坏的事件，该怎么办？
- 如果 Functions 实例遇到了未经处理的异常，该怎么办？
- 如果下游系统脱机，该怎么办？

如何在保持应用程序吞吐量的情况下处理这些情况？

对于队列，可靠消息传递可以自然而然地进行。 与 Functions 触发器搭配使用时，函数会在队列消息中创建一个锁。 如果处理失败，将释放该锁，使另一个实例能够重试处理。 然后，处理将会继续，直到成功评估消息或者将消息添加到有害队列为止。

即使单个队列消息仍保留在重试循环中，其他并行执行也会继续取消剩余消息的排队。 结果是，整体吞吐量在很大程度上不受一条错误消息的影响。 但是，存储队列不能保证处理顺序，并且未针对事件中心的高吞吐量需求进行优化。

相反，Azure 事件中心不存在锁定的概念。 为了实现高吞吐量、多个使用者组和重播等功能，事件中心事件的行为更像是视频播放器。 按分区从流中的单个点读取事件。 可以从指针位置向前或向后读取，但必须选择移动要处理的事件的指针。

当流中出现错误时，如果你决定将指针保留在同一个点，则在指针递进之前，事件处理将受到阻止。 换而言之，如果停止指针来解决处理单个事件时出现的问题，则未处理的事件将开始累积。

无论成功与否，Azure Functions 都会通过递进流的指针来避免死锁。 由于指针不断递进，函数需要相应地处理故障。

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Azure Functions 如何使用事件中心事件

Azure Functions 在循环执行以下步骤的同时使用事件中心事件：

1. 为事件中心的每个分区创建一个指针并将其保存在 Azure 存储中。
2. 收到新消息（默认分批接收）时，宿主会尝试对消息批触发函数。
3. 如果该函数完成执行（出现或未出现异常），则指针将会递进，并将一个检查点保存到存储帐户。
4. 如果某种状况阻止该函数完成执行，则宿主无法递进指针。 如果指针未递进，则后面的检查会结束相同消息的处理。
5. 重复步骤 2-4

此行为揭示了几个要点：

- 未经处理的异常可能导致丢失消息。 导致异常的执行会继续递进指针。
- 函数保证至少传送一次。 代码和相关系统可能需要[考虑到同一消息可能会接收两次这一事实](./functions-idempotent.md)。

## <a name="handling-exceptions"></a>处理异常

作为一般规则，每个函数应在代码的最高级别包含一个 [try/catch 块](./functions-bindings-error-pages.md)。 具体而言，使用事件中心事件的所有函数都应有一个 `catch` 块。 这样，在引发异常时，catch 块会在指针递进之前处理错误。

### <a name="retry-mechanisms-and-policies"></a>重试机制和策略

某些异常在性质上是暂时性的，稍后再次尝试操作时不会重现。 正因如此，第一个步骤始终是重试操作。 你可以自行编写重试处理规则，不过，有许多现成的工具可以完成此工作。 使用这些库可以定义可靠的重试策略，而这些策略也有助于保持处理顺序。

将故障处理库引入函数可以定义基本和高级重试策略。 例如，可以实现一个遵循以下规则所演示的工作流的策略：

- 尝试插入某条消息三次（可以在每次重试之前设置一定的延迟）。
- 如果所有重试的最终结果均为失败，则将一条消息添加到队列，以便可以在流中继续进行处理。
- 然后将处理损坏的或未处理的消息。

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) 是适用于 C# 应用程序的复原和暂时性故障处理库的一个示例。

使用预编译的 C# 类库时，每当发生未经处理的异常，都可以借助[异常筛选器](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch)来运行代码。

[Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 存储库中提供了演示如何使用异常筛选器的示例。

## <a name="non-exception-errors"></a>非异常错误

有些问题即使在未出错时也会出现。 例如，假设在执行的中途发生失败。 在这种情况下，如果某个函数未完成执行，则偏移指针永远不会递进。 如果指针未递进，则在执行失败后运行的任何实例将继续读取相同的消息。 这种机制可提供“至少一次”保证。

保证每条消息至少被处理一次意味着，某些消息可能会被处理多次。 函数应用需要意识到这种可能性，并且必须围绕[幂等性原则](./functions-idempotent.md)构建。

## <a name="stop-and-restart-execution"></a>停止和重启执行

尽管少量的错误可能是可接受的，但如果应用遇到严重的故障，该怎么办？ 在系统达到正常状态之前，你可能想要停止基于事件的触发。 通常可以通过断路器模式来实现处理暂停。 断路器模式允许应用“中断”事件进程的线路，稍后再恢复。

在事件进程中实现断路器需要两项信息：

- 所有实例之间用于跟踪和监视线路运行状况的共享状态
- 可管理线路状态（打开或闭合）的主进程

实现详细信息可能有所不同，但对于实例之间的共享状态，需要使用一个存储机制。 可以选择将状态存储在 Azure 存储、Redis 缓存或者可由函数集合访问的任何其他帐户中。

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)或[持久实体](./durable/durable-functions-overview.md)原生就很适合用于管理工作流和线路状态。 其他服务可能也适用，不过，本示例使用逻辑应用。 使用逻辑应用时，可以暂停和重启函数的执行，以便能够控制断路器模式的实现。

### <a name="define-a-failure-threshold-across-instances"></a>定义实例之间的故障阈值

若要同时考虑多个实例处理事件，需要保存共享的外部状态以监视线路的运行状况。

可以选择实现的规则可能会强制要求：

- 如果在 30 秒内所有实例的最终故障数超过 100，则中断线路，并针对新消息停止触发。

实现详细信息根据需求而异，但一般情况下，可以创建一个系统来执行以下操作：

1. 将故障记录到存储帐户（Azure 存储、Redis 等）
1. 记录新的故障时，检查累积计数以查看是否符合阈值（例如，过去 30 秒内超过 100 个）。
1. 如果符合阈值，则将一个事件发送到 Azure 事件网格，告知系统中断线路。

### <a name="managing-circuit-state-with-azure-logic-apps"></a>使用 Azure 逻辑应用管理线路状态

以下内容重点介绍了一种创建 Azure 逻辑应用来停止函数应用的处理的方式。

Azure 逻辑应用随附了不同服务的内置连接器，提供有状态业务流程，并且是管理线路状态的自然选择。 检测到线路需要中断后，可以构建一个逻辑应用来实现以下工作流：

1. 触发事件网格工作流并停止 Azure 函数（使用 Azure 资源连接器）
1. 发送通知电子邮件并在其中包含用于重启工作流的选项

电子邮件收件人可以调查线路的运行状况，并在适当的情况下通过通知电子邮件中的链接重启线路。 当工作流重启函数时，将从最后一个事件中心检查点处理消息。

使用此方法不会丢失任何消息、会按顺序处理所有消息，并可以根据需要中断线路。

## <a name="resources"></a>资源

- [可靠事件处理的示例](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure Durable Functions 断路器](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

- [Azure Functions 错误处理](./functions-bindings-error-pages.md)
- [使用事件网格自动调整已上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [创建与 Azure 逻辑应用集成的函数](./functions-twitter-email.md)
