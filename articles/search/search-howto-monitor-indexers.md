---
title: 监视索引器状态和结果
titleSuffix: Azure Cognitive Search
description: 使用 REST API 或 .NET SDK 监视 Azure 门户中 Azure 认知搜索索引器的状态、进度和结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112974"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>如何监视 Azure 认知搜索索引器状态和结果

Azure 认知搜索提供有关每个索引器的当前和历史运行的状态和监视信息。

想要执行以下操作时，索引器监视非常有用：

* 在运行期间跟踪索引器的进度。
* 查看正在运行的或上一个索引器运行的结果。
* 找出顶级索引器错误，以及有关要编制索引的单个文档的错误或警告。

## <a name="get-status-and-history"></a>获取状态和历史记录

可通过多种方式访问索引器监视信息，包括：

* 在 [Azure 门户](#portal)中
* 使用 [REST API](#restapi)
* 使用 [.NET SDK](#dotnetsdk)

提供的索引器监视信息包括以下各项（不过，数据格式根据所用的访问方法而异）：

* 有关索引器本身的状态信息
* 有关索引器的最近运行的信息，包括其状态、开始和结束时间，以及详细的错误和警告。
* 历史索引器运行的列表及其状态、结果、错误和警告。

处理大量数据的索引器可能需要很长时间才能完成运行。 例如，处理数百万个源文档的索引器可能要运行 24 个小时，紧接着重启。 处理大量数据的索引器的状态可能始终在门户中显示为“正在进行”。 即使索引器正在运行，也会提供有关当前进度和以前的运行的详细信息。

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>使用门户进行监视

可以在搜索服务“概述”页上的“索引器”列表中查看所有索引器的当前状态。

   ![索引器列表](media/search-monitor-indexers/indexers-list.png "索引器列表")

索引器正在执行时，列表中的状态将显示为“正在进行”，“成功的文档数”值将显示到目前为止已处理的文档数。 门户可能需要花费几分钟时间来更新索引器状态值和文档计数。

其最近运行成功的索引器将显示“成功”。 如果错误数小于索引器的“最大失败项数”设置，即使单个文档出错，索引器运行也可以成功。

如果最近的运行最终出错，状态将显示为“失败”。 “重置”状态表示索引器的更改跟踪状态已重置。

单击列表中的某个索引器可以查看有关该索引器的当前运行和最近运行的更多详细信息。

   ![索引器摘要和执行历史记录](media/search-monitor-indexers/indexer-summary.png "索引器摘要和执行历史记录")

“索引器摘要”图表显示最近运行中处理的文档数图表。

“执行详细信息”列表最多显示 50 个最近执行结果。

单击该列表中的某个执行结果可查看有关该运行的具体信息， 包括该运行的开始和结束时间，以及发生的任何错误和警告。

   ![索引器执行详细信息](media/search-monitor-indexers/indexer-execution.png "索引器执行详细信息")

如果在运行期间出现文档特定的问题，这些问题将在“错误”和“警告”字段中列出。

   ![包含错误的索引器详细信息](media/search-monitor-indexers/indexer-execution-error.png "包含错误的索引器详细信息")

警告经常会出现在某些类型的索引器上，不一定表示出现了问题。 例如，当图像或 PDF 文件不包含任何有待处理的文本时，使用认知服务的索引器可能会报告警告。

有关调查索引器错误和警告的详细信息，请参阅[排查 Azure 中的常见索引器问题认知搜索](search-indexer-troubleshooting.md)。

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>使用 REST Api 进行监视

可以使用[“获取索引器状态”命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)检索索引器的状态和执行历史记录：

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

执行历史记录最多包含 50 个最近的运行，这些已按逆时间顺序排序（最近的运行排在最前面）。

请注意有两个不同的状态值。 顶级状态适用于索引器本身。 索引器状态“正在运行”表示索引器已正确设置且可运行，而不是当前正在运行。

每次运行索引器时，还会显示该索引器自身的状态，指示特定的执行是正在进行（“正在运行”），还是已完成并且状态为“成功”、“暂时性失败”或“永久性失败”。 

重置索引器以刷新其更改跟踪状态时，将添加一个“重置”状态的独立执行历史记录条目。

有关状态代码和索引器监视数据的详细信息，请参阅 [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>使用 .NET SDK 进行监视

可以使用 Azure 认知搜索 .NET SDK 定义索引器的计划。 为此，请在创建或更新索引器时包含 **schedule** 属性。

以下 C# 示例将有关索引器状态及其最近（或正在运行）运行的结果的信息写入控制台。

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

控制台中的输出如下所示：

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

请注意有两个不同的状态值。 顶级状态是索引器本身的状态。 索引器状态“正在运行”表示索引器已正确设置且可执行，而不是当前正在执行。

每次运行索引器时，还会显示该索引器自身的状态，指示特定的执行是正在进行（“正在运行”），还是已完成并且状态为“成功”或“暂时性错误”。 

重置索引器以刷新其更改跟踪状态时，将添加一个“重置”状态的独立历史记录条目。

有关状态代码和索引器监视信息的详细信息，请参阅 REST API 中的 [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

可以通过枚举 `IndexerExecutionResult.Errors` 和 `IndexerExecutionResult.Warnings` 列表来检索有关文档特定的错误或警告的详细信息。

有关用于监视索引器的 .NET SDK 类的详细信息，请参阅 [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) 和 [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)。
