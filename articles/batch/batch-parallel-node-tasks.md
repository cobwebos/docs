---
title: 以并发方式运行任务以最大程度地利用 Batch 计算节点
description: 使用更少的计算节点和在 Azure Batch 池中的每个节点上并行运行任务，提高效率并降低成本
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102959"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>以并发方式运行任务以最大程度地利用 Batch 计算节点

可以通过在每个节点上同时运行多个任务来最大限度地减少池中的计算节点上的资源使用率。

尽管有些方案最适用于专用于单个任务的节点资源，但在多个任务共享这些资源时，某些工作负荷可能会发现更短的作业时间并降低成本：

- **最小化** 可共享数据的任务的数据传输。 可以通过将共享数据复制到较少数量的节点，然后在每个节点上并行执行任务来大幅减少数据传输费用。 尤其是在复制到每个节点的数据必须跨地理区域传输的情况下。
- **最大程度地提高** 需要大量内存的任务的内存使用情况，但只能在短时间内，并在执行过程中使用可变时间。 可以减少计算节点的数量但增加其大小，同时提供更多的内存，以便有效地应对此类高峰负载。 这些节点会在每个节点上并行运行多个任务，而每个任务都会充分利用节点在不同时间的大量内存。
- 当池中需要节点间通信时，请**缓解节点号限制**。 目前，经过配置可以进行节点间通信的池仅限 50 个计算节点。 如果此类池中的每个节点都可以并行执行任务，则可同时执行较大数量的任务。
- **复制本地计算群集**，如首次将计算环境移到 Azure 时。 如果当前本地解决方案在单个计算节点上执行多个任务，则可以通过增大节点任务的最大数量来更彻底地对配置进行镜像操作。

## <a name="example-scenario"></a>示例方案

例如，假设有一个具有 CPU 和内存要求的任务应用程序，以便 [标准 \_ D1](../cloud-services/cloud-services-sizes-specs.md) 节点足够。 但是，若要在所需时间内完成作业，则需要1000个节点。

如果不使用具有 1 个 CPU 内核的 Standard\_D1 节点，则可使用每个具有 16 个内核的 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) 节点，同时允许并行执行任务。 这意味着可以使用 *16 倍的节点* ，而不是1000节点，只需要63。 如果每个节点需要大型应用程序文件或引用数据，作业持续时间和效率将再次得到提升，因为数据仅复制到63节点。

## <a name="enable-parallel-task-execution"></a>允许并行执行任务

可在池级别配置计算节点，以便并行执行任务。 使用 Batch .NET 库时，请在创建池时设置 [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 属性。 如果使用的是批处理 REST API，请在创建池时在请求正文中设置 [taskSlotsPerNode](/rest/api/batchservice/pool/add) 元素。

> [!NOTE]
> `taskSlotsPerNode`只能在创建池时设置元素和[TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool)属性。 在创建池后，不能对其进行修改。

Azure Batch 允许你将每个节点的任务槽设置为 (4x) 节点核心数。 例如，如果将池的节点大小配置为“大型”（四核），则可将 `taskSlotsPerNode` 设置为 16。 但是，无论节点有多少个核心，每个节点不能有超过256的任务槽。 有关每个节点大小的核心数的详细信息，请参阅[云服务的大小](../cloud-services/cloud-services-sizes-specs.md)。 有关服务限制的详细信息，请参阅 [Azure Batch 服务的配额和限制](batch-quota-limit.md)。

> [!TIP]
> 为池构造[自动缩放公式](/rest/api/batchservice/pool/enableautoscale)时，请务必考虑 `taskSlotsPerNode` 值。 例如，如果增加每个节点的任务数，则可能会极大地影响对 `$RunningTasks` 求值的公式。 有关详细信息，请参阅 [自动缩放 Azure Batch 池中的计算节点](batch-automatic-scaling.md)。

## <a name="specify-task-distribution"></a>指定任务分发

当启用并发任务时，必须指定如何在池中的节点之间分配任务。

可以通过 [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) 属性指定任务，即让任务在池中所有节点之间平均分配（“散布式”）。 或者，先给池中的每个节点分配尽量多的任务，再将任务分配给池中的其他节点（“装箱式”）。

例如，在上面的示例中，请考虑 [标准 \_ D14](../cloud-services/cloud-services-sizes-specs.md) 节点 (，该池) 配置为16的 [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 值。 如果 CloudPool 配置了 [cloudpool.taskschedulingpolicy](/dotnet/api/microsoft.azure.batch.cloudpool) 的 [computenodefilltype 设置](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) ，则它可以最大程度地使用每个节点的所有16个 *核心，并*允许自动 [缩放池](batch-automatic-scaling.md) (节点中删除未使用的节点，而无需从池中分配任何任务) 。 这可以最大程度地减少资源使用量并节省资金。

## <a name="define-variable-slots-per-task"></a>定义每个任务的可变槽

可以使用 [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) 属性定义任务，指定在计算节点上运行所需的槽数。 默认值为1。 如果任务的权重与计算节点上的资源使用情况不同，可以设置变量任务槽。 这使每个计算节点都有合理数量的并发运行任务，而不会产生大量的系统资源，例如 CPU 或内存。

例如，对于具有属性的池 `taskSlotsPerNode = 8` ，你可以使用提交多核需要的大量 CPU 任务 `requiredSlots = 8` ，而其他任务可以设置为 `requiredSlots = 1` 。 计划此混合工作负荷时，CPU 密集型任务将以独占方式在其计算节点上运行，而其他任务可以同时 (多达八个任务) 在其他节点上运行。 这有助于平衡计算节点上的工作负荷，提高资源使用效率。

> [!TIP]
> 使用变量任务槽时，可能会暂时无法计划具有更多所需槽的大型任务，因为任何计算节点上都没有足够的可用槽，即使某些节点上仍有空闲的槽。 你可以提高这些任务的优先级，增加对节点上可用槽的竞争机会。
>
> 当批处理服务未能计划要运行的任务时，它会发出 [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) ，并在所需的槽可用之前继续重试计划。 你可以侦听该事件以检测潜在的任务计划问题，并相应地进行缓解。

> [!NOTE]
> 不要将任务的指定 `requiredSlots` 为大于池的 `taskSlotsPerNode` 。 这将导致任务永远无法运行。 当你提交任务时，批处理服务当前不会验证此冲突，因为作业可能在提交时未绑定到池，也可以通过禁用/重新启用将其更改为不同的池。

## <a name="batch-net-example"></a>Batch .NET 示例

以下 [批处理 .Net](/dotnet/api/microsoft.azure.batch) API 代码片段演示了如何创建一个每个节点具有多个任务槽的池，以及如何使用所需的槽提交任务。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>为每个节点创建具有多个任务槽的池

此代码段演示了创建包含四个节点的池的请求，每个节点都允许四个任务槽。 它指定了一个任务计划策略，要求先用任务填充一个节点，然后再将任务分配给池中的其他节点。

有关如何使用 Batch .NET API 添加池的详细信息，请参阅 [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations)。

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>使用所需的槽创建任务

此代码片段将创建一个非默认的任务 `requiredSlots` 。 仅当计算节点上有足够的可用槽时，此任务才会运行。

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>列出正在运行的任务和槽的计算节点

此代码段列出池中的所有计算节点，并输出每个节点正在运行的任务和任务槽的计数。

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>列出作业的任务计数

此代码段获取作业的任务计数，其中包含任务和任务槽每个任务状态的计数。

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST 示例

以下 [批处理 REST](/rest/api/batchservice/) API 代码段显示了如何创建一个每个节点具有多个任务槽的池，以及如何使用所需的槽提交任务。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>为每个节点创建具有多个任务槽的池

此代码片段演示了如何创建包含两个大型节点的池，每个节点最多四个任务。

有关如何使用 REST API 添加池的详细信息，请参阅[将池添加到帐户](/rest/api/batchservice/pool/add)。

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>使用所需的槽创建任务

此代码片段演示了添加非默认任务的请求 `requiredSlots` 。 仅当计算节点上有足够的可用槽时，此任务才会运行。

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>GitHub 上的代码示例

GitHub 上的 [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 项目说明了如何使用 [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 属性。

此 C# 控制台应用程序使用 [Batch .NET](/dotnet/api/microsoft.azure.batch) 库创建包含一个或多个计算节点的池。 它在这些节点上执行可配置的任务数来模拟可变负载。 应用程序的输出显示执行每个任务的节点。 该应用程序还提供了作业参数和持续时间的摘要。

例如，下面是 ParallelTasks 示例应用程序的两个不同运行的输出的摘要部分。 此处显示的作业持续时间不包括创建池的时间，因为每个作业都提交到以前创建的池，其计算节点在提交时处于 *空闲* 状态。

第一次执行示例应用程序时，结果显示，在池中只有一个节点且使用默认的一个节点一个任务设置的情况下，作业持续时间超过 30 分钟。

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

第二次运行示例应用程序时，显示作业持续时间显著缩短。 这是因为该池配置为每个节点四个任务，从而允许并行任务执行在大约一个季度内完成该作业。

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>后续步骤

- 尝试 [Batch Explorer](https://azure.github.io/BatchExplorer/) 热度地图。 Batch Explorer 是一个功能丰富的免费独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 当您执行 [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) 示例应用程序时，Batch Explorer 热度地图功能可让您轻松地可视化每个节点上并行任务的执行情况。
- 探索 [GitHub 上的 Azure Batch 示例](https://github.com/Azure/azure-batch-samples)。
- 详细了解 [批处理任务依赖关系](batch-task-dependencies.md)。
