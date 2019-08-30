---
title: 如何计划索引器-Azure 搜索
description: 计划 Azure 搜索索引器, 以便定期或在特定时间为内容编制索引。
ms.date: 05/31/2019
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.openlocfilehash: f72067637f9db84a432562ea5502861355426469
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186573"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>如何为 Azure 搜索计划索引器
索引器在创建后立即运行一次。 可以使用门户、REST API 或 .NET SDK 按需再次运行该示例。 你还可以将索引器配置为定期按计划运行。

索引器计划的某些情况很有用:

* 源数据将随着时间的推移而更改, 并且你希望 Azure 搜索索引器自动处理已更改的数据。
* 索引将从多个数据源进行填充, 并且你希望确保索引器在不同时间运行以减少冲突。
* 源数据非常大, 你希望随着时间的推移分散索引器处理。 有关对大量数据进行索引的详细信息, 请参阅[如何在 Azure 搜索中为大型数据集编制索引](search-howto-large-index.md)。

计划程序是 Azure 搜索的内置功能。 不能使用外部计划程序来控制搜索索引器。

## <a name="define-schedule-properties"></a>定义计划属性

索引器计划有两个属性:
* **间隔**, 定义计划索引器执行之间的时间量。 允许的最小间隔为5分钟, 最大间隔为24小时。
* **开始时间 (UTC)** , 指示首次运行索引器的时间。

您可以在首次创建索引器时指定计划, 或在以后更新索引器的属性。 可以使用[门户](#portal)、 [REST API](#restApi)或[.net SDK](#dotNetSdk)设置索引器计划。

一次只能运行一个索引器执行。 如果在计划下一次执行时索引器已在运行, 则该执行将推迟到下一个计划的时间。

让我们考虑更具体的示例。 假设我们配置索引器计划, 其**间隔**为每小时,**开始时间**为 8:00:00 2019 年6月1日, 即 UTC。 下面是当索引器运行时间超过一小时时可能出现的情况:

* 第一个索引器执行的开始时间为2019年6月1日上午 8:00 AM UTC。 假设此执行需要 20 分钟（或小于 1 小时的任何时间）。
* 第二次执行的开始时间为2019年6月1日上午 9:00 AM UTC。 假设此执行耗时超过70分钟, 并且在 10:10 AM UTC 之前不会完成。
* 第三次执行计划为在 10:00 AM UTC 开始, 但此时仍在运行。 然后, 将跳过此计划的执行。 索引器的下一次执行会在 11:00 AM UTC 之前开始。

> [!NOTE]
> 如果索引器设置为某个计划, 但每次运行时在同一文档上重复失败, 则索引器将以不太频繁的间隔 (最多每24小时至少一次) 运行, 直到它成功完成进度 aga中.  如果你认为已修复了导致索引器停留在某个时间点的任何问题, 则可以执行索引器的按需运行, 如果成功, 则索引器将再次返回到其设置的计划时间间隔。

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>在门户中定义计划

使用门户中的 "导入数据" 向导, 您可以在创建时定义索引器的计划。 默认计划设置为 "**每小时**", 这意味着索引器在创建后运行一次, 并在每小时之后再次运行。

如果你不希望自动运行索引器, 则可以将 "计划" 设置更改为 "**一次**", 或者更改为 "**每天**" 运行一次。 如果要指定其他间隔或特定的未来开始时间, 请将它设置为 "**自定义**"。

将计划设置为 "**自定义**" 时, 将显示字段, 使您可以指定**时间间隔**和**开始时间 (UTC)** 。 允许的最短时间间隔为5分钟, 最长为1440分钟 (24 小时)。

   ![在导入数据向导中设置索引器计划](media/search-howto-schedule-indexers/schedule-import-data.png "在导入数据向导中设置索引器计划")

创建索引器后, 可以使用索引器的编辑面板更改计划设置。 "计划" 字段与 "导入数据" 向导中的字段相同。

   ![在索引器编辑面板中设置计划](media/search-howto-schedule-indexers/schedule-edit.png "在索引器编辑面板中设置计划")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>使用 REST API 定义计划

您可以使用 REST API 定义索引器的计划。 为此, 请在创建或更新索引器时包括**schedule**属性。 下面的示例演示了用于更新现有索引器的 PUT 请求:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**间隔**参数是必需的。 间隔是指开始两个连续的索引器执行之间的时间。 允许的最小间隔为 5 分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`P(nD)(T(nH)(nM))`。 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。

可选**startTime**指示何时应开始执行计划的执行。 如果省略，则使用当前 UTC 时间。 这一时间可以是过去的时间, 在这种情况下, 将计划第一次执行, 就像索引器自初始**startTime**后连续运行一样。

你还可以使用运行索引器调用随时按需运行索引器。 有关运行索引器和设置索引器计划的详细信息, 请参阅在 REST API 引用中[运行索引器](https://docs.microsoft.com/rest/api/searchservice/run-indexer)、[获取索引](https://docs.microsoft.com/rest/api/searchservice/get-indexer)器和[更新索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer)。

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>使用 .NET SDK 定义计划

可以使用 Azure 搜索 .NET SDK 定义索引器的计划。 为此, 请在创建或更新索引器时包括**schedule**属性。

以下C#示例使用预定义的数据源和索引创建一个索引器, 并将其计划设置为每天从现在30分钟运行一次:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
如果省略**schedule**参数, 索引器将在创建后立即运行一次。

**StartTime**参数可以设置为过去的某个时间。 在这种情况下, 将计划第一次执行, 就像索引器在给定**startTime**后连续运行一样。

计划是使用[IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet)类定义的。 **IndexingSchedule**构造函数需要使用**TimeSpan**对象指定的**间隔**参数。 允许的最小间隔值为5分钟, 最大间隔为24小时。 指定为**DateTimeOffset**对象的第二个**startTime**参数是可选的。

.NET SDK 允许使用[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)类和其 "[索引器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers)" 属性 (实现**IIndexersOperations**接口中的方法) 来控制索引器操作。 

您可以使用[run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run)、 [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)或[RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)方法之一随时按需运行索引器。

有关创建、更新和运行索引器的详细信息, 请参阅[IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)。
