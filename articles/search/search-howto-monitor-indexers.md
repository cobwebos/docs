---
title: 如何监视索引器状态和结果-Azure 搜索
description: 使用 REST API 或 .NET SDK 监视 Azure 门户中 Azure 搜索索引器的状态、进度和结果。
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 52e9e96598f429bcd57bba23d035d0d341731a9c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840734"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>如何监视 Azure 搜索索引器状态和结果

Azure 搜索提供了有关每个索引器的当前和历史运行的状态和监视信息。

当你想要执行以下操作时, 索引器监视非常有用:

* 跟踪索引器在正在进行的运行期间的进度。
* 查看正在运行的或上一个索引器运行的结果。
* 标识顶级索引器错误, 以及有关要编制索引的单个文档的错误或警告。

## <a name="find-indexer-status-and-history-details"></a>查找索引器状态和历史记录详细信息

可以通过多种方式访问索引器监视信息, 其中包括:

* 在 [Azure 门户](#portal)中
* 使用[REST API](#restapi)
* 使用[.NET SDK](#dotnetsdk)

可用的索引器监视信息包含以下所有项 (尽管数据格式根据所使用的访问方法而异):

* 有关索引器本身的状态信息
* 有关最新的索引器运行的信息, 包括其状态、开始和结束时间以及详细的错误和警告。
* 历史索引器运行的列表及其状态、结果、错误和警告。

处理大量数据的索引器可能需要很长时间才能运行。 例如, 处理数百万个源文档的索引器可运行24小时, 然后几乎立即重新启动。 大容量索引器的状态可能始终会在门户中显示为 "**正在进行**"。 即使正在运行索引器, 详细信息也可用于正在进行的进度和以前的运行。

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>在门户中监视索引器

您可以在 "搜索服务概述" 页上的 "**索引器**" 列表中查看所有索引器的当前状态。

   ![索引器列表](media/search-monitor-indexers/indexers-list.png "索引器列表")

执行索引器时, 列表中的状态显示为 "**正在进行**", "**文档成功**" 值显示迄今为止处理的文档数。 门户更新索引器状态值和文档计数可能需要几分钟时间。

最近运行成功的索引器显示**成功**。 如果错误数小于索引器的 "**最大失败项目**数" 设置, 则即使个别文档出现错误, 索引器运行也可能成功。

如果最新的运行已结束, 但出现错误, 则状态显示为 "**失败**"。 **重置**状态表示索引器的更改跟踪状态已重置。

单击列表中的索引器可查看有关索引器的当前运行和最新运行的详细信息。

   ![索引器摘要和执行历史记录](media/search-monitor-indexers/indexer-summary.png "索引器摘要和执行历史记录")

**索引器摘要**图表显示在最近的运行中处理的文档数的图表。

"**执行详细信息**" 列表最多显示最新执行结果的50。

在列表中单击某个执行结果可查看有关该运行的详细信息。 这包括其开始和结束时间以及发生的任何错误和警告。

   ![索引器执行详细信息](media/search-monitor-indexers/indexer-execution.png "索引器执行详细信息")

如果在运行过程中存在特定于文档的问题, 则这些问题将在 "错误" 和 "警告" 字段中列出。

   ![包含错误的索引器详细信息](media/search-monitor-indexers/indexer-execution-error.png "包含错误的索引器详细信息")

警告是某些类型的索引器所共有的, 并不总是指示存在问题。 例如, 使用认知服务的索引器可以在图像或 PDF 文件不包含任何要处理的文本时报告警告。

有关调查索引器错误和警告的详细信息, 请参阅[Azure 搜索中常见索引器问题的疑难解答](search-indexer-troubleshooting.md)。

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>使用 REST API 监视索引器

您可以使用[获取索引器状态命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)检索索引器的状态和执行历史记录:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

响应包含总体索引器状态、最后一次（或正在进行的）索引器调用以及最近索引器调用的历史记录。

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

执行历史记录最多包含50个最近运行的运行, 这些运行以逆序顺序 (最近的第一个) 排序。

请注意, 有两个不同的状态值。 顶级状态为索引器本身。 索引器状态为 "**正在运行**" 表示索引器设置正确且可供运行, 但不是当前正在运行。

每次运行该索引器时, 都有自己的状态, 该状态指示特定执行是正在进行 (**正在运行**), 还是已完成并且具有**success**、 **transientFailure**或**persistentFailure**状态。 

当重置索引器以刷新其更改跟踪状态时, 将添加一个具有**重置**状态的单独的执行历史记录项。

有关状态代码和索引器监视数据的详细信息, 请参阅[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>使用 .NET SDK 监视索引器

可以使用 Azure 搜索 .NET SDK 定义索引器的计划。 为此, 请在创建或更新索引器时包括**schedule**属性。

下面C#的示例将有关索引器状态以及其最新运行 (或正在运行) 的结果的信息写入控制台。

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

控制台中的输出将如下所示:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

请注意, 有两个不同的状态值。 顶级状态为索引器本身的状态。 索引器状态为 "**正在运行**" 意味着索引器设置正确且可用于执行, 但不是当前正在执行的。

每次运行该索引器时, 都有自己的状态, 即特定执行正在进行 (**正在运行**), 还是已完成, 状态为 "**成功**" 或 " **TransientError** "。 

当重置索引器以刷新其更改跟踪状态时, 将添加一个具有**重置**状态的单独的历史记录项。

有关状态代码和索引器监视信息的详细信息, 请参阅 REST API 中的[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 。

可以通过枚举列表`IndexerExecutionResult.Errors`和`IndexerExecutionResult.Warnings`来检索有关文档特定错误或警告的详细信息。

有关用于监视索引器的 .NET SDK 类的详细信息, 请参阅[IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet)和[IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)。
