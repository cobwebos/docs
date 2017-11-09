---
title: "通过按状态进行任务计数的方式监视作业进度 - Azure Batch | Microsoft Docs"
description: "通过调用“获取任务计数”操作对作业的任务进行计数，监视作业进度。 可以对处于活动状态、正在运行或已完成的任务计数，也可以对成功或失败的任务计数。"
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>按状态对任务计数，监视作业进度（预览）

Azure Batch 提供一种有效的方法，在作业运行任务时监视作业进度。 可以通过调用[获取任务计数][rest_get_task_counts]操作找出处于活动状态、正在运行或已完成的任务数，也可以找出已成功或失败的任务数。 通过对每种状态的任务计数，可以更轻松地为用户展现作业进度，或检测可能影响作业的意外延迟或故障。

> [!IMPORTANT]
> “获取任务计数”操作目前仅供预览，Azure Government、Azure China 和 Azure Germany 中尚不提供该功能。 
>
>

## <a name="how-tasks-are-counted"></a>如何进行任务计数

“获取任务计数”操作按状态进行任务计数，如下所示：

- 任务已排队且能够运行（但目前没有分配到计算节点）时，将它视为处于“活动”状态。 如果任务依赖于尚未完成的父任务，将它视为处于“活动”状态。 有关任务依赖关系的详细信息，请参阅[创建任务依赖关系，以运行依赖于其他任务的任务](batch-task-dependencies.md)。 
- 任务分配到计算节点但尚未完成时，将它视为“正在运行”。 当任务状态为 `preparing` 或 `running` 时，将它视为“正在运行”，正如[获取有关任务的信息][rest_get_task]操作所示。
- 当任务不再符合运行条件时，将它视为“完成”。 通常情况下，视作“完成”的任务或者已成功完成，或者未成功完成并已达到重试次数限制。 

“获取任务计数”操作还报告成功或失败的任务数。 Batch 通过检查 [executionInfo][https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo] 属性的“结果”属性判定任务的成败：

    - 如果任务执行的结果是 `success`，则将该任务视为“成功”。
    - 如果任务执行的结果是 `failure`，则将该任务视为“失败”。

有关任务状态的详细信息，请参阅[获取有关任务的信息][rest_get_task]。

下方的 .NET 代码示例演示如何按状态检索任务计数： 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> 可以对 REST 和支持的其他语言使用类似的模式获取作业的任务计数。 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>任务计数的一致性检查

Batch 服务通过从异步分布式系统的多个部分收集数据，聚合任务计数。 为了确保任务计数正确，Batch 通过对系统的多个组件执行一致性检查，为状态计数提供额外验证。 只要作业中的任务数少于 200,000，Batch 就执行这些一致性检查。 极少数情况下，一致性检查会发现错误，此时 Batch 根据一致性检查的结果更正“获取任务计数”操作的结果。 一致性检查是一项额外措施，用于确保依赖“获取任务计数”操作的用户为他们的解决方案获取所需的正确信息。

响应中的“validationStatus”属性表示 Batch 是否已执行一致性检查。 如果 Batch 尚不能检查系统保留的实际状态的状态计数，则将“validationStatus”属性设置为 `unvalidated`。 出于性能原因，当作业包含的任务数超过 200,000 项时，Batch 不执行一致性检查，所以在此情况下，将“validationStatus”属性设置为 `unvalidated`。 然而，此情况中的任务计数并不一定错误，因为发生数据丢失（即使是极少量数据丢失）的可能性微乎其微。 

任务更改状态时，聚合管道在几秒内处理该更改。 “获取任务计数”操作在该期间体现更新后的任务计数。 然而，如果聚合管道错过任务状态更改，则直到下一次验证通过才会记录该更改。 在此期间，由于错过的事件，任务计数可能不太准确，但将在下一次验证通过时更正计数。

## <a name="best-practices-for-counting-a-jobs-tasks"></a>对作业任务进行计数的最佳做法

调用“获取任务计数”操作是按状态返回作业任务基本计数最有效的方法。 如果使用的是 Batch 服务 2017-06-01.5.1 版，建议写入或更新代码，以便使用“获取任务计数”。

早于 2017-06-01.5.1 版的 Batch 服务不提供“获取任务计数”操作。 如果使用的是该服务的较旧版本，请改用列表查询对作业中的任务计数。 有关详细信息，请参阅[创建可高效列出 Batch 资源的查询](batch-efficient-list-queries.md)。

## <a name="next-steps"></a>后续步骤

* 请参阅[批处理功能概述](batch-api-basics.md)，详细了解处理服务的概念和功能。 本文讨论主要 Batch 资源（例如池、计算节点、作业和任务）并提供该服务的功能概述。
* 了解使用[批处理 .NET 客户端库](batch-dotnet-get-started.md)或 [Python](batch-python-tutorial.md) 开发支持批处理的应用程序的基本概念。 这些简介文章介绍了使用 Batch 服务在多个计算节点上执行工作负荷的可行应用程序。


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
