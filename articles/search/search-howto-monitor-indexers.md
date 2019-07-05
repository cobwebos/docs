---
title: 如何监视索引器状态和结果-Azure 搜索
description: 监视状态、 进度和结果使用 REST API 或.NET SDK 在 Azure 门户中的 Azure 搜索索引器。
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486280"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>如何监视 Azure 搜索索引器状态和结果

Azure 搜索提供状态和监视的每个索引器的当前和历史运行有关的信息。

根据需要，则索引器监视非常有用：

* 运行跟踪过程正在进行的索引器的进度。
* 查看正在进行或上一个索引器运行的结果。
* 标识顶级索引器错误和错误或要编制索引的单个文档有关的警告。

## <a name="find-indexer-status-and-history-details"></a>找到索引器状态和历史记录的详细信息

您可以访问各种用途，包括索引器监视信息：

* 在 [Azure 门户](#portal)中
* 使用[REST API](#restapi)
* 使用[.NET SDK](#dotnetsdk)

监视信息的可用索引器包括以下所有 （不过，根据使用的访问方法将数据格式的区别）：

* 有关索引器本身的状态信息
* 有关最新信息运行索引器，包括其状态、 开始和结束时间和详细的错误和警告。
* 历史记录的索引器运行时，其状态、 结果、 错误和警告的列表。

处理数据量很大的索引器可能需要很长时间才能运行。 例如，处理数以百万计的源文档的索引器可以运行 24 小时，然后几乎立即重新启动。 大量索引器的状态可能会始终说**正在进行中**在门户中。 即使运行索引器，提供有关正在进行的进度和之前运行了详细信息。

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>在门户中的监视器索引器

可以看到所有应用中的索引器的当前状态**索引器**你搜索服务概述页上的列表。

   ![索引器列表](media/search-monitor-indexers/indexers-list.png "索引器列表")

执行索引器时，列表将显示状态**正在进行中**，并**Docs 成功**值显示到目前为止处理的文档数。 可能需要几分钟后，门户更新索引器状态值和文档计数。

索引器的最新运行已成功显示**成功**。 运行索引器可以成功即使单个文档具有错误，如果错误数小于索引器的**最大失败项目**设置。

如果最新运行结束并出现错误，状态将显示**失败**。 状态**重置**意味着已重置索引器的更改跟踪状态。

单击要查看有关索引器的详细信息当前和最新的列表中的索引器上运行。

   ![索引器摘要和执行历史记录](media/search-monitor-indexers/indexer-summary.png "索引器摘要和执行历史记录")

**索引器摘要**图表显示在其最新的运行中处理的文档数的关系图。

**执行详细信息**列表显示了最多 50 个最新的执行结果。

单击列表以查看有关运行的具体信息中的某个执行结果。 这包括其开始和结束时间和任何错误以及出现的警告。

   ![索引器执行详细信息](media/search-monitor-indexers/indexer-execution.png "索引器执行详细信息")

如果在运行期间没有特定于文档的问题，它们将列出的错误和警告字段中。

   ![错误的详细信息，索引器](media/search-monitor-indexers/indexer-execution-error.png "错误的详细信息，索引器")

警告通常可以使用某些类型的索引器，并不始终表示存在问题。 例如使用认知服务的索引器可以报告警告时图像或 PDF 文件不包含任何文本要处理。

有关调查索引器错误和警告的详细信息，请参阅[排查 Azure 搜索中常见的索引器问题](search-indexer-troubleshooting.md)。

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>监视索引器使用 REST API

您可以检索的索引器使用的状态和执行历史记录[获取索引器状态命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

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

执行历史记录包含最多 50 倒序顺序 （最新的第一个） 中的排序的最新运行。

请注意有两个不同状态的值。 索引器本身是最高级别状态。 索引器状态**运行**意味着索引器设置正确并且可在运行，但不是它的当前运行。

每次运行索引器也有其自己的状态，指示该特定执行是否正在进行 (**运行**)，或已完成，但有**成功**， **transientFailure**，或**persistentFailure**状态。 

重置索引器以刷新其更改跟踪状态，请添加一个单独的执行历史记录条目**重置**状态。

有关状态代码和索引器监视数据的更多详细信息，请参阅[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>监视索引器使用.NET SDK

可以定义索引器使用 Azure 搜索.NET SDK 的计划。 若要执行此操作，包括**计划**属性创建或更新索引器时。

以下C#的示例将有关索引器的状态信息写入并向控制台运行其最新 （或正在进行） 的结果。

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

在控制台输出将如下所示：

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

请注意有两个不同状态的值。 顶级状态是索引器本身的状态。 索引器状态**运行**意味着索引器已正确设置并可供执行，但它不当前正在执行。

每次运行索引器也有其自己的该特定执行是否正在进行的状态 (**运行**)，或者与已完成**成功**或**TransientError**状态。 

重置索引器以刷新其更改跟踪状态，请使用添加一个单独的历史记录条目**重置**状态。

有关状态代码和监视信息的索引器的更多详细信息，请参阅[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) REST API 中。

可以通过枚举列表来检索有关特定于文档的错误或警告的详细信息`IndexerExecutionResult.Errors`和`IndexerExecutionResult.Warnings`。

有关用来监视索引器的.NET SDK 类的详细信息，请参阅[IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet)并[IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)。
