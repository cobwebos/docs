---
title: "为 Azure 搜索的 Cosmos DB 数据源编制索引 | Microsoft Docs"
description: "本文介绍如何使用 Cosmos DB 作为数据源创建 Azure 搜索索引器。"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 08/10/2017
ms.author: eugenesh
ms.openlocfilehash: 2f1791393b1e59721cc5a1030927cd00d74a5f13
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>使用索引器连接 Cosmos DB 和 Azure 搜索

如果想获得极佳的 Cosmos DB 数据搜索体验，可使用 Azure 搜索索引器将数据提取到 Azure 搜索索引中。 本文中将展示如何将 Azure Cosmos DB 与 Azure 搜索进行集成且无需编写代码以保持编制基础结构的索引。

若要设置 Cosmos DB 索引器，必须具有 [Azure 搜索服务](search-create-service-portal.md)，并依次创建索引、数据源和索引器。 可以使用[门户](search-import-data-portal.md)、[.NET SDK](/dotnet/api/microsoft.azure.search) 或适用于所有非. NET 语言的 [REST API](/rest/api/searchservice/) 创建这些对象。 

如果选择使用门户，[导入数据向导](search-import-data-portal.md)将引导完成所有这些资源的创建操作。

> [!NOTE]
> Cosmos DB 是下一代 DocumentDB。 虽然更改了产品名称，但语法与之前相同。 请继续按照此索引器文章的指示指定 `documentdb`。 

> [!TIP]
> 可从 Cosmos DB 仪表板启动“导入数据”向导，进而简化该数据源的索引。 在左侧导航栏中，转到“集合” > “添加 Azure 搜索”开始操作。

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure 搜索索引器概念
Azure 搜索支持创建和管理数据源（包括 Cosmos DB）以及针对这些数据源操作的索引器。

**数据源**指定要编制索引的数据、凭据和用于识别数据更改（如修改或删除了集合内的文档）的策略。 数据源定义为独立的资源，以便它可以被多个索引器使用。

**索引器**描述数据从数据源流动到目标搜索索引的方式。 索引器可用于：

* 执行数据的一次性复制以填充索引。
* 在计划上同步索引与数据源中的更改。 计划是索引器定义的一部分。
* 根据需要调用对索引的按需更新。

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源
若要创建数据源，请执行 POST 操作：

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

请求正文包含数据源定义，其中应包括以下字段：

* 名称：选择任意名称来表示 Cosmos DB 数据库。
* **类型**：必须为 `documentdb`。
* **凭据**：
  
  * **connectionString**：必需。 采用以下格式指定 Azure Cosmos DB 数据库的连接信息：`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **容器**：
  
  * **名称**：必需。 指定要编制索引的 Cosmos DB 集合的 ID。
  * **查询**：可选。 可以指定一个查询来将一个任意 JSON 文档平整成 Azure 搜索可编制索引的平面架构。
* **dataChangeDetectionPolicy**：推荐。 请参阅[为已更改的文档编制索引](#DataChangeDetectionPolicy)部分。
* **dataDeletionDetectionPolicy**：可选。 请参阅[为已删除的文档编制索引](#DataDeletionDetectionPolicy)部分。

### <a name="using-queries-to-shape-indexed-data"></a>使用查询形成索引数据
可指定 Cosmos DB 查询平展嵌套的属性或数组、投影 JSON 属性并筛选要编制索引的数据。 

示例文档：

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

筛选查询：

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

平展查询：

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
投影查询：

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


数组平展查询：

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>步骤 2：创建索引
如果还没有目标 Azure 搜索索引，请创建一个。 可使用 [Azure 门户 UI](search-create-index-portal.md)、[创建索引 REST API](/rest/api/searchservice/create-index) 或[索引类](/dotnet/api/microsoft.azure.search.models.index)来创建索引。

下面的示例创建带有 ID 和描述字段的索引：

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

确保目标索引的架构与源 JSON 文档的架构或自定义查询投影的输出的架构兼容。

> [!NOTE]
> 对于分区集合，默认文档键是 Cosmos DB 的 `_rid` 属性，它在 Azure 搜索中重命名为 `rid`。 此外，Cosmos DB 的 `_rid` 值包含 Azure 搜索键中无效的字符。 因此，`_rid` 值采用 Base64 编码。
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON 数据类型与 Azure 搜索数据类型之间的映射
| JSON 数据类型 | 兼容的目标索引字段类型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 类似于整数的数字 |Edm.Int32、Edm.Int64、Edm.String |
| 类似于浮点的数字 |Edm.Double、Edm.String |
| String |Edm.String |
| 基元类型的数组，如 ["a", "b", "c"] |集合 (Edm.String) |
| 类似于日期的字符串 |Edm.DateTimeOffset、Edm.String |
| GeoJSON 对象，如 { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 其他 JSON 对象 |不适用 |

<a name="CreateIndexer"></a>
## <a name="step-3-create-an-indexer"></a>步骤 3：创建索引器

创建索引和数据源后，就可以准备创建索引器了：

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

此索引器每两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>按需运行索引器
除按计划定期运行以外，也可按需调用索引器：

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> “运行 API”成功返回时，已计划索引器调用，但实际处理过程以异步方式发生。 

可在门户中或使用“获取索引器状态 API”（会在接下来的内容中介绍）监视索引器状态。 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>获取索引器状态
可检索索引器的状态和执行历史记录：

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

响应包含总体索引器状态、最后一次（或正在进行的）索引器调用以及最近索引器调用的历史记录。

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

执行历史记录包含最多 50 个最近完成的执行，它们被按反向时间顺序排序（因此，最新执行出现在响应中的第一个）。

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>为已更改的文档编制索引
数据更改检测策略旨在有效识别已更改的数据项。 目前，唯一支持的策略是 Cosmos DB 提供的使用 `_ts`（时间戳）属性的 `High Water Mark` 策略，它按如下所示指定：

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

强烈建议使用此策略，以确保索引器性能良好。 

如果使用自定义查询，请确保查询投影 `_ts` 属性。

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>增量操作和自定义查询
索引编制过程中的增量操作可确保由于暂时性故障或执行时间限制而中断索引器执行时，索引器能够在下次运行时从中断位置运行，而不是从头开始重新为整个集合编制索引。 在为大型集合编制索引时，这一点尤其重要。 

要在使用自定义查询时启用增量操作，请确保查询按照 `_ts` 列对结果进行排序。 这会启用定期检查点，在出现故障时，Azure 搜索可以利用检查点提供增量操作。   

在某些情况下，即使查询包含 `ORDER BY [collection alias]._ts` 子句，Azure 搜索也可能不会推断出查询是按照 `_ts` 进行排序的。 可以告知 Azure 搜索，结果是通过使用 `assumeOrderByHighWaterMarkColumn` 配置属性进行排序的。 要指定此提示，请按如下所示创建或更新索引器： 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>为已删除的文档编制索引
从集合中删除行时，通常还需要从搜索索引中删除这些行。 数据删除检测策略旨在有效识别已删除的数据项。 目前，唯一支持的策略是 `Soft Delete` 策略（删除标有某种类型的标志），它按如下所示指定：

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

如果使用自定义查询，请确保查询投影由 `softDeleteColumnName` 引用的属性。

下面的示例创建具有软删除策略的数据源：

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>后续步骤
祝贺你！ 已学习了如何使用 Azure Cosmos DB 索引器将 Cosmos DB 与 Azure 搜索进行集成。

* 若要了解有关 Azure Cosmos DB 的详细信息，请参阅 [Cosmos DB 服务页](https://azure.microsoft.com/services/documentdb/)。
* 若要了解有关 Azure 搜索的详细信息，请参阅[搜索服务页](https://azure.microsoft.com/services/search/)。
