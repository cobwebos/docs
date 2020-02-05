---
title: 设计有效的列表查询 - Azure Batch | Microsoft Docs
description: 在请求批处理资源（例如池、作业、任务和计算节点）的相关信息时对查询进行筛选可提高性能。
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df923ac479ce5f5a3668c18c616b11348dc6c0b3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022233"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>创建可高效列出 Batch 资源的查询

在此，您将了解如何通过减少在使用[批处理 .net][api_net]库查询作业、任务、计算节点和其他资源时服务返回的数据量，提高您 Azure Batch 应用程序的性能。

几乎所有批处理应用程序都需执行某类监视操作或其他查询批处理服务的操作（通常按固定的时间间隔）。 例如，若要确定作业中是否还有排队的任务，必须获取作业中每个任务的相关数据。 若要确定池中节点的状态，必须获取池中每个节点的相关数据。 本文介绍如何以最有效方式执行此类查询。

> [!NOTE]
> Batch 服务为作业中的任务计数以及 Batch 池中的计算节点计数这类常见方案提供特殊 API 支持。 可以调用 "[获取任务计数][rest_get_task_counts]" 和 "[列出池节点计数][rest_get_node_counts]" 操作，而不是使用列表查询。 这些操作比列表查询更高效，但返回的信息更有限。 请参阅[按状态对任务和计算节点计数](batch-get-resource-counts.md)。 


## <a name="meet-the-detaillevel"></a>符合 DetailLevel 要求
在生产型批处理应用程序中，作业、任务和计算节点等实体的数目成千上万。 请求这些资源的相关信息时，可能需要将大量的数据从 Batch 服务“跨网络”传输到执行每个查询的应用程序。 通过限制查询时返回的项数和信息类型，可以提高查询速度，因此也会提高应用程序的性能。

此[Batch .Net][api_net] API 代码片段列出与作业关联的*每*个任务，以及每个任务的*所有*属性：

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

但是，用户可以通过向查询应用“详细信息级别”，执行效率高得多的列表查询。 为此，请将[ODATADetailLevel][odata]对象提供给[joboperations.listtasks][net_list_tasks]方法。 此代码段仅返回已完成任务的 ID、命令行和计算节点信息属性：

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

在此示例方案中，如果作业中存在数以千计的任务，则通常情况下，第二个查询的结果的返回速度将远远快于第一个查询。 [下面](#efficient-querying-in-batch-net)提供了有关使用 Batch .NET API 列出项时使用 ODATADetailLevel 的详细信息。

> [!IMPORTANT]
> 强烈建议*始终* 将 ODATADetailLevel 对象提供给 .NET API 列表调用，确保最大程度地提高应用程序的效率和性能。 指定详细程度有助于缩短 Batch 服务响应时间、提高网络利用率，以及最大程度减少客户端应用程序的内存使用量。
> 
> 

## <a name="filter-select-and-expand"></a>Filter、select 和 expand
使用[batch .net][api_net]和[batch REST][api_rest] api 可以减少列表中返回的项目数，以及为每个项目返回的信息量。 在执行列表查询时可以通过指定 **filter**、**select** 和 **expand** 字符串来实现此目的。

### <a name="filter"></a>筛选
filter 字符串是一个表达式，用于减少返回的项数。 例如，只列出作业的运行中任务，或者只列出已做好运行任务准备的计算节点。

* filter 字符串包含一个或多个表达式，其中一个表达式包含属性名称、运算符和值。 能够指定哪些属性取决于所查询的每个实体类型，每个属性所支持的运算符也是这样。
* 可以使用逻辑运算符 `and` 和 `or` 将多个表达式组合到一起。
* 此示例性 filter 字符串仅列出正在运行的“呈现”任务：`(state eq 'running') and startswith(id, 'renderTask')`。

### <a name="select"></a>选择
select 字符串用于限制为每个项返回的属性值。 可以指定属性名称的列表，仅在查询结果中返回项目的这些属性值。

* select 字符串包含逗号分隔的属性名称列表。 可以指定所查询实体类型的任意属性。
* 此示例性的 select 字符串指定每个任务只应返回三项属性值：`id, state, stateTransitionTime`。

### <a name="expand"></a>展开
expand 字符串用于减少获取特定信息所需的 API 调用数。 使用 expand 字符串时，单次 API 调用可以获取每个项目的更多信息。 不必首先获取实体的列表，然后请求列表中每个项目的信息。可以使用 expand 字符串通过单次 API 调用获取相同的信息。 API 调用数较少意味着性能较高。

* 与 select 字符串类似，expand 字符串用于控制是否允许某些数据包括在列表查询结果中。
* expand 字符串只有在列出作业、作业计划、任务和池中使用时才受支持。 目前仅支持统计信息。
* 当所有属性都是必需属性且没有指定 select 字符串时，*必须* 使用 expand 字符串来获取统计信息。 如果使用了 select 字符串来获取属性的子集，则可在 select 字符串中指定 `stats`，不需指定 expand 字符串。
* 此示例性 expand 字符串指定列表中的每个项都应返回统计信息：`stats`。

> [!NOTE]
> 构造这三种查询字符串类型（filter、select 和 expand）中的任意一种类型时，必须确保属性名称和大小写与其 REST API 元素的对应项相匹配。 例如，在使用 .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) 类时，必须指定 **state** 而非 **State**，即使 .NET 属性为 [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State)。 请参阅下表中 .NET 和 REST API 之间的属性映射。
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>filter、select 和 expand 字符串的规则
* Filter、select 和 expand 字符串中属性名称的显示方式应与其在[BATCH REST][api_rest] API 中的显示方式相同，即使使用的是[batch .net][api_net]或其他某个批处理 sdk 时也是如此。
* 所有属性名称均区分大小写，但属性值不区分大小写。
* 日期/时间字符串可以采用两种格式中的一种，并且必须在前面加上 `DateTime`。
  
  * W3C-DTF 格式示例：`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 格式示例：`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* 布尔值字符串为 `true` 或 `false`。
* 如果指定了无效的属性或运算符，则会导致 `400 (Bad Request)` 错误。

## <a name="efficient-querying-in-batch-net"></a>在 Batch .NET 中进行高效查询
在[Batch .Net][api_net] API 中， [ODATADetailLevel][odata]类用于提供筛选器，选择并展开字符串以列出操作。 ODataDetailLevel 类有三个公共字符串属性，这些属性可以在构造函数中指定，也可以直接在对象上设置。 然后，将 ODataDetailLevel 对象作为参数传递到各种列表操作，例如[ListPools][net_list_pools]、 [ListJobs][net_list_jobs]和[joboperations.listtasks][net_list_tasks]。

* [ODATADetailLevel][odata]。[FilterClause][odata_filter]：限制返回的项数。
* [ODATADetailLevel][odata]。[Odatadetaillevel.selectclause][odata_select]：指定每个项返回的属性值。
* [ODATADetailLevel][odata]。[ExpandClause][odata_expand]：通过单个 API 调用检索所有项的数据，而不是对每个项单独调用。

以下代码段使用 Batch .NET API 对 Batch 服务进行有效的查询，查询其中是否存在特定池集的统计信息。 在此方案中，Batch 用户既有测试池又有生产池。 测试池 ID 具有“test”前缀，生产池 ID 具有“prod”前缀。 在代码片段中，*myBatchClient* 是正确初始化的 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 类实例。

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> 使用 Select 和 Expand 子句配置的[ODATADetailLevel][odata]实例还可以传递到适当的 Get 方法（如[PoolOperations](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)），以限制返回的数据量。
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST 到 .NET API 映射
filter、select 和 expand 字符串中的属性名称*必须*反映其 REST API 对应项，不管是名称本身还是大小写。 下表提供了 .NET 和 REST API 的对应项之间的映射。

### <a name="mappings-for-filter-strings"></a>filter 字符串的映射
* **.Net 列表方法**：此列中的每个 .net API 方法都接受[ODATADetailLevel][odata]对象作为参数。
* **REST 列表请求**：此列中的每个 REST API 页面都包含一个表，该表指定了*filter* 字符串中允许的属性和操作。 构造[ODATADetailLevel FilterClause][odata_filter]字符串时，将使用这些属性名称和操作。

| .NET 列表方法 | REST 列表请求 |
| --- | --- |
| [Certificateoperations.createcertificate. ListCertificates][net_list_certs] |[列出帐户中的证书][rest_list_certs] |
| [CloudTask. 调用 cloudtask.listnodefiles][net_list_task_files] |[列出与任务关联的文件][rest_list_task_files] |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[列出作业的作业准备和作业释放任务的状态][rest_list_jobprep_status] |
| [JobOperations. ListJobs][net_list_jobs] |[列出帐户中的作业][rest_list_jobs] |
| [JobOperations. 调用 cloudtask.listnodefiles][net_list_nodefiles] |[列出节点上的文件][rest_list_nodefiles] |
| [JobOperations. Joboperations.listtasks][net_list_tasks] |[列出与作业关联的任务][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[列出帐户中的作业计划][rest_list_job_schedules] |
| [JobScheduleOperations. ListJobs][net_list_schedule_jobs] |[列出与作业计划关联的作业][rest_list_schedule_jobs] |
| [PoolOperations. ListComputeNodes][net_list_compute_nodes] |[列出池中的计算节点][rest_list_compute_nodes] |
| [PoolOperations. ListPools][net_list_pools] |[列出帐户中的池][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>select 字符串的映射
* **Batch .NET 类型**：Batch .NET API 类型。
* **REST API 实体**：此列中的每一页都包含一个或多个表，其中列出了类型的 REST API 属性名称。 在构造 *select* 字符串时使用这些属性名称。 构造[ODATADetailLevel. odatadetaillevel.selectclause][odata_select]字符串时，将使用这些相同的属性名。

| Batch .NET 类型 | REST API 实体 |
| --- | --- |
| [证书][net_cert] |[获取有关证书的信息][rest_get_cert] |
| [CloudJob][net_job] |[获取有关作业的信息][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[获取有关作业计划的信息][rest_get_schedule] |
| [ComputeNode][net_node] |[获取有关节点的信息][rest_get_node] |
| [CloudPool][net_pool] |[获取有关池的信息][rest_get_pool] |
| [CloudTask][net_task] |[获取有关任务的信息][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>示例：构造 filter 字符串
构造[ODATADetailLevel][odata_filter]的筛选器字符串时，请参考 "筛选字符串的映射" 下面的表，查找与要执行的列表操作相对应的 "REST API 文档" 页。 会在该页第一个多行表中找到可筛选属性及其支持的运算符。 例如，如果希望检索其退出代码为非零值的所有任务，请在[列表中列出与作业关联的任务][rest_list_tasks]，指定适用的属性字符串和允许的运算符：

| 属性 | 允许的操作 | 类型 |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

因此，若要列出退出代码不为零的所有任务，可使用以下 filter 字符串：

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>示例：构造 select 字符串
若要构造[ODATADetailLevel][odata_select]，请参考上面的 "用于选择字符串的映射" 下面的表，并导航到与所列出的实体类型相对应的 "REST API" 页。 会在该页第一个多行表中找到可选择属性及其支持的运算符。 例如，如果希望仅检索列表中每个任务的 ID 和命令行，可在[获取有关任务的信息][rest_get_task]的相应表中找到这些行：

| 属性 | 类型 | 说明 |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

因此，若要只包括每个所列任务的 ID 和命令行，可使用以下 select 字符串：

`id, commandLine`

## <a name="code-samples"></a>代码示例
### <a name="efficient-list-queries-code-sample"></a>高效列表查询代码示例
查看 GitHub 上的[EfficientListQueries][efficient_query_sample]示例项目，了解列表查询如何有效地影响应用程序的性能。 此 C# 控制台应用程序创建大量的任务并将其添加到作业。 然后，它对[JobOperations][net_list_tasks]方法进行多次调用，并传递用不同属性值配置的[ODATADetailLevel][odata]对象，以改变要返回的数据量。 生成的输出如下所示：

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

如所用时间中所示，限制返回的属性和项数可以大大缩短查询响应时间。 可在 GitHub 上的[azure 批处理][github_samples]存储库中找到此项目和其他示例项目。

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics 库和代码示例
除了上述 EfficientListQueries 代码示例，还可以在[azure 批处理][github_samples]GitHub 存储库中找到[BatchMetrics][batch_metrics]项目。 BatchMetrics 示例项目演示了如何使用批处理 API 有效地监视 Azure Batch 作业进度。

[BatchMetrics][batch_metrics]示例包括一个 .net 类库项目，可将其合并到自己的项目中，并使用简单的命令行程序执行并演示库的使用。

项目中的该示例应用程序演示了以下操作：

1. 选择特定属性即可只下载所需属性
2. 若只下载自上次查询以来发生更改的内容，可针对状态转换时间进行筛选

例如，以下方法会显示在 BatchMetrics 库中。 它会返回 ODATADetailLevel，指定仅针对所查询的实体获取 `id` 和 `state` 属性。 它还指定只应返回其状态自指定 `DateTime` 参数以来发生更改的实体。

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>后续步骤
### <a name="parallel-node-tasks"></a>并行节点任务
[通过并发节点任务最大限度提高 Azure Batch 计算资源的使用率](batch-parallel-node-tasks.md)是另一篇与批处理应用程序性能相关的文章。 在数量较少但规模更大的计算节点上执行并行任务适合某些类型的工作负荷。 若需详细了解此类方案，请查看文章中的[示例方案](batch-parallel-node-tasks.md#example-scenario)。


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
