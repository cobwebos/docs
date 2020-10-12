---
title: 对任务和节点的状态进行计数
description: 对 Azure Batch 任务和计算节点的状态进行计数，以便管理和监视 Batch 解决方案。
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85960582"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>通过按状态对任务和节点计数来监视 Batch 解决方案

若要监视和管理大规模 Azure Batch 解决方案，可能需要确定处于各种状态的资源的计数。 Azure Batch 提供有效的操作来获取 Batch 任务和计算节点的计数。 可使用这些操作而不是可能非常耗时的列表查询来返回大型任务或节点集合的详细信息。

- [获取任务计数](/rest/api/batchservice/job/gettaskcounts)可以获取一个作业中处于“活动”、“正在运行”和“已完成”状态的任务以及处于“已成功”或“已失败”状态的任务的聚合计数。 

  通过对每种状态的任务计数，可以更轻松地为用户展现作业进度，或检测可能影响作业的意外延迟或故障。 “获取任务计数”功能在 Batch Service API 版本 2017-06-01.5.1 以及相关的 SDK 和工具中提供。

- [列出池节点计数](/rest/api/batchservice/account/listpoolnodecounts)获取每个池中处于不同状态（“正在创建”、“空闲”、“脱机”、“已占用”、“正在重启”、“正在重置映像”、“正在启动”、“其他”）的专用计算节点和低优先级计算节点的数目。

  通过对每种状态的节点计数，你就可以确定是否有足够的计算资源来运行作业，并确定池可能存在的问题。 “列出池节点计数”功能在 Batch Service API 版本 2018-03-01.6.1 以及相关的 SDK 和工具中提供。

请注意，在这种情况下，这些操作返回的数字可能不是最新的。 如果需要确保计数是准确的，请使用列表查询对这些资源进行计数。 使用列表查询还可以获取其他 Batch 资源（例如应用程序）的信息。 若要详细了解如何将筛选器应用于列表查询，请参阅[创建可高效列出 Batch 资源的查询](batch-efficient-list-queries.md)。

## <a name="task-state-counts"></a>任务状态计数

“获取任务计数”操作按以下状态进行任务计数：

- **活动** - 任务已排队且能够运行，但目前没有分配到计算节点。 如果任务所[依赖的父任务](batch-task-dependencies.md)尚未完成，则该任务也处于`active`状态。 
- **正在运行** - 任务已分配到计算节点但尚未完成。 当任务状态为`preparing`或`running`时，将它视为`running`，正如[获取有关任务的信息](/rest/api/batchservice/task/get)操作所示。
- **已完成** - 任务不再有资格运行，因为已成功完成，或者虽未成功完成但已达到其重试次数限制。 
- **已成功** - 执行结果为`success`的任务。 Batch 通过检查 [executionInfo](/rest/api/batchservice/task/get) 属性的 `TaskExecutionResult` 属性来确定任务是已成功还是已失败。
- **已失败** - 执行结果为`failure`的任务。

下方的 .NET 代码示例演示如何按状态检索任务计数：

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

可以对 REST 和支持的其他语言使用类似的模式获取作业的任务计数。 

> [!NOTE]
> 2018-08-01.7.0 之前的 Batch Service API 也会在 Get Task Counts 响应中返回一个 `validationStatus` 属性。 此属性表示 Batch 是否已检查状态计数是否与 List Tasks API 中报告的状态一致。 `validated` 的值表示 Batch 至少为作业检查了一次一致性。 `validationStatus` 属性的值不表示 Get Task Counts 返回的计数当前是否是最新的。

## <a name="node-state-counts"></a>节点状态计数

“列出池节点计数”操作按以下状态对每个池中的计算节点进行计数。 会对每个池中的专用节点和低优先级节点进行单独的聚合计数。

- **正在创建** - Azure 分配的 VM 尚未开始加入池。
- **空闲** - 目前未运行任务的可用计算节点。
- **正在离开池** - 节点正在离开池，可能是因为用户显式删除了它，或者是因为池正在重设大小或者正在进行向下自动缩放。
- **脱机** - Batch 无法用来计划 新任务的节点。
- **已占用** - 由于 Azure 回收了 VM，低优先级节点被从池中删除。 如果系统提供替代性的低优先级 VM 容量，则`preempted`节点可以重新初始化。
- **正在重启** - 节点正在重启。
- **正在重置映像** - 操作系统正在节点上重新安装。
- **正在运行** - 节点正在运行一个或多个任务（不是启动任务）。
- **正在启动** - Batch 服务正在节点上启动。 
- 启动任务已失败 - 节点上的[启动任务](/rest/api/batchservice/pool/add#starttask)已失败，已达到重试次数限制，并且已在启动任务上设置 `waitForSuccess`。 此节点不可用于运行任务。
- **未知** - 节点失去与 Batch 服务的联系，其状态未知。
- **不可使用** - 节点因错误而不能用于执行任务。
- **等待启动任务** - 节点上的启动任务已开始运行，但是设置了 `waitForSuccess`，启动任务尚未完成。

以下 C# 代码片段演示如何列出当前帐户中所有池的节点计数：

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

以下 C# 代码片段演示如何列出当前帐户中给定池的节点计数。

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

可以对 REST 和支持的其他语言使用类似的模式获取池的节点计数。

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)（如池、节点、作业和任务）。
- 了解如何将筛选器应用于列出 Batch 资源的查询，请参阅[创建可高效列出 Batch 资源的查询](batch-efficient-list-queries.md)。
