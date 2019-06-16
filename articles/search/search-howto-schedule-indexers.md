---
title: 如何计划索引器-Azure 搜索
description: 计划对索引内容的 Azure 搜索索引器定期或在特定时间。
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755375"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>如何计划 Azure 搜索索引器
索引器在创建后立即正常运行一次。 您可以使用门户、 REST API 或.NET SDK 按需再次运行它。 此外可以配置索引器以按计划定期运行。

某些情况下，索引器计划时很有用：

* 源数据会随时间，并且您希望 Azure 搜索索引器可自动处理已更改的数据。
* 将多个数据源填充索引，并且你想要确保索引器运行在不同的时间来减少冲突。
* 源数据量非常大，并且你想要传播索引器处理随着时间的推移。 有关索引的大量数据的详细信息，请参阅[如何在 Azure 搜索中的大型数据集编制索引](search-howto-large-index.md)。

计划程序是 Azure 搜索的内置功能。 不能使用外部计划程序来控制搜索索引器。

## <a name="define-schedule-properties"></a>定义计划属性

索引器计划有两个属性：
* **间隔**，之间定义的时间计划索引器执行。 允许的最小间隔为 5 分钟，并最大为 24 小时。
* **启动时 (UTC)** ，指示应在其中运行索引器的第一个时间。

首次创建索引器时或更高版本更新索引器的属性，可以指定一个计划。 可以使用设置索引器计划[门户](#portal)，则[REST API](#restApi)，或[.NET SDK](#dotNetSdk)。

一次只能运行一个索引器执行。 如果已在其下一次执行计划时运行索引器，该执行将推迟到下一个计划时间。

让我们考虑更具体的示例。 假设我们配置索引器计划，其中**间隔**的每小时和一个**开始时间**的 2019 年 6 月 1 日上午 8:00:00 utc。 下面是运行索引器花费的时间超过一小时时，可能发生什么情况：

* 第一次索引器执行启动或大约 2019 年 6 月 1 日上午 8:00 utc。 假设此执行需要 20 分钟（或小于 1 小时的任何时间）。
* 第二次执行启动或大约上午 9:00 2019 年 6 月 1 日 UTC。 假设此执行需要 70 分钟-超过一小时 – 它将无法完成之前 UTC 时间上午 10:10。
* 第三个执行计划在上午 10:00 UTC 开始，但在该时间上一次执行仍在运行。 此计划然后跳过执行。 之前 UTC 时间上午 11:00，不会开始的索引器的下一次执行。

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>在门户中定义的计划

在门户中的导入数据向导，可以在创建时定义索引器计划。 默认计划设置是**每小时**，这意味着索引器运行一次之后它将创建并运行再次每隔一小时之后。

您可以将计划设置更改为**一旦**如果不想要再次自动运行的索引器或设置为**每日**以每天运行一次。 将其设置为**自定义**如果你想要指定其他时间间隔或特定将来开始时间。

将计划设置为**自定义**，使你能够指定显示字段**间隔**并**开始时间 (UTC)** 。 允许的间隔为 5 分钟，且时间最长的时间最短为 1440 分钟 （24 小时）。

   ![在导入数据向导中设置索引器计划](media/search-howto-schedule-indexers/schedule-import-data.png "导入数据向导中的设置索引器计划")

创建索引器后，你可以使用索引器的编辑面板的计划设置。 计划字段是与导入数据向导中的相同。

   ![索引器在编辑面板中设置计划](media/search-howto-schedule-indexers/schedule-edit.png "索引器在编辑面板中设置计划")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>使用 REST API 将计划定义

可以定义索引器使用 REST API 的计划。 若要执行此操作，包括**计划**属性创建或更新索引器时。 下面的示例显示了用于更新现有索引器的 PUT 请求：

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**间隔**参数是必需的。 间隔是指开始两个连续的索引器执行之间的时间。 允许的最小间隔为 5 分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`P(nD)(T(nH)(nM))`。 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。

可选**startTime**指示应开始计划的执行的时间。 如果省略，则使用当前 UTC 时间。 这一次可以是过去，因为如果具有原始之后连续运行索引器用例第一次执行计划**startTime**。

此外可以按需在任何时候使用运行索引器调用运行索引器。 有关运行索引器和设置索引器计划的详细信息，请参阅[运行索引器](https://docs.microsoft.com/rest/api/searchservice/run-indexer)，[获取索引器](https://docs.microsoft.com/rest/api/searchservice/get-indexer)，并[更新索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer)REST API 参考。

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>使用.NET SDK 将计划定义

可以定义索引器使用 Azure 搜索.NET SDK 的计划。 若要执行此操作，包括**计划**属性创建或更新索引器时。

以下C#示例创建一个索引器，使用一个预定义的数据源和索引，并设置其计划运行从现在开始 30 分钟内每日一次：

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
如果**计划**省略参数、 索引器将只运行一次，在创建后立即。

**StartTime**参数可设置过去的时间。 在这种情况下，计划第一次执行像索引器运行连续开始从给定**startTime**。

使用定义计划[IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet)类。 **IndexingSchedule**构造函数需要**间隔**使用指定的参数**TimeSpan**对象。 允许的最小时间间隔值为 5 分钟，并最大为 24 小时。 第二个**startTime**参数，指定为**DateTimeOffset**对象，则可选。

.NET SDK，可以使用的控件索引器操作[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)类并将其[索引器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers)属性，用于实现从方法**IIndexersOperations**接口。 

您可以在任何时候使用之一根据需要运行索引器[运行](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run)， [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)，或[RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)方法。

有关创建的详细信息，更新和运行索引器，请参阅[IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)。
