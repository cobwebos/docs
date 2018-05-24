---
title: 为 Azure 搜索的 Azure Cosmos DB 数据源编制索引 | Microsoft Docs
description: 本文演示如何使用 Azure Cosmos DB 数据源创建 Azure 搜索索引器。
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: a724057981b5b389011ffc4c2fc93994c2b8be9e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777485"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>使用索引器连接 Cosmos DB 和 Azure 搜索

在本文中，将学习以下内容：

> [!div class="checklist"]
> * 配置使用 Azure Cosmos DB 集合作为数据源的 [Azure 搜索索引器](search-indexer-overview.md)。
> * 创建具有 JSON 兼容数据类型的搜索索引。
> * 为按需和定期索引配置索引器。
> * 基于基础数据中的更改以增量方式刷新索引。

> [!NOTE]
> Azure Cosmos DB 是下一代 DocumentDB。 虽然产品名称已更改，但无论是在 Azure 搜索 API 中还是在门户页面中，Azure 搜索索引器中的 `documentdb` 语法都仍然存在以实现向后兼容。 在配置索引器时，请务必按本文中的说明指定 `documentdb` 语法。

在下面的视频中，Azure Cosmos DB 计划经理 Andrew Liu 演示了如何将 Azure 搜索索引添加到 Azure Cosmos DB 容器。

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>支持的 API 类型

虽然 Azure Cosmos DB 支持各种数据模型和 API，但 Azure 搜索索引器生产支持仅扩展到了 SQL API。 对 MongoDB API 的支持目前处于公开预览状态。  

即将推出对其他 API 的支持。 若要帮助我们确定优先支持哪些 API，请在 User Voice 网站上投票：

* [表 API 数据源支持](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [图形 API 数据源支持](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Apache Cassandra API 数据源支持](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>先决条件

除了 Cosmos DB 帐户外，还必须具备 [Azure 搜索服务](search-create-service-portal.md)。 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure 搜索索引器概念

**数据源**指定要编制索引的数据、凭据和用于识别数据更改（如修改或删除了集合内的文档）的策略。 数据源定义为独立的资源，以便它可以被多个索引器使用。

**索引器**描述数据从数据源流动到目标搜索索引的方式。 索引器可用于：

* 执行数据的一次性复制以填充索引。
* 在计划上同步索引与数据源中的更改。
* 根据需要调用对索引的按需更新。

若要设置 Azure Cosmos DB 索引器，需要依次创建索引、数据源和索引器。 可以使用[门户](search-import-data-portal.md)、[.NET SDK](/dotnet/api/microsoft.azure.search) 或 [REST API](/rest/api/searchservice/) 创建这些对象。 

本文演示如何使用 REST API。 如果选择使用门户，[导入数据向导](search-import-data-portal.md)将引导完成所有这些资源（包括索引）的创建操作。

> [!TIP]
> 可从 Azure Cosmos DB 仪表板启动**导入数据**向导，进而简化该数据源的索引。 在左侧导航栏中，转到“集合” > “添加 Azure 搜索”开始操作。

> [!NOTE] 
> 目前，无法使用 Azure 门户或 .NET SDK 创建或编辑 **MongoDB** 数据源。 但是，**可以**在门户中监视 MongoDB 索引器的执行历史记录。  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源
若要创建数据源，请执行 POST 操作：

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

请求正文包含数据源定义，其中应包括以下字段：

* **名称**：选择任意名称来表示你的数据库。
* **类型**：必须为 `documentdb`。
* **凭据**：
  
  * **connectionString**：必需。 按以下格式指定 Azure Cosmos DB 数据库的连接信息：`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`；对于 MongoDB 集合，将 **ApiKind=MongoDB** 添加到连接字符串：`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDB` 
* **容器**：
  
  * **名称**：必需。 指定要编制索引的数据库集合的 ID。
  * **查询**：可选。 可以指定一个查询来将一个任意 JSON 文档平整成 Azure 搜索可编制索引的平面架构。 对于 MongoDB 集合，不支持查询。 
* **dataChangeDetectionPolicy**：推荐。 请参阅[为已更改的文档编制索引](#DataChangeDetectionPolicy)部分。
* **dataDeletionDetectionPolicy**：可选。 请参阅[为已删除的文档编制索引](#DataDeletionDetectionPolicy)部分。

### <a name="using-queries-to-shape-indexed-data"></a>使用查询形成索引数据
可以指定一个 SQL 查询来平展嵌套的属性或数组、投影 JSON 属性并筛选要编制索引的数据。 

> [!WARNING]
> **MongoDB** 集合不支持自定义查询：`container.query` 参数必须设置为 null 或省略。 如果需要使用自定义查询，请在[用户之声](https://feedback.azure.com/forums/263029-azure-search)上告知我们。

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

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
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
> 对于已分区集合，默认文档键是 Azure Cosmos DB 的 `_rid` 属性，Azure 搜索会自动将其重命名为 `rid`，因为字段名称不能以下划线字符开头。 此外，Azure Cosmos DB 的 `_rid` 值包含了在 Azure 搜索键中无效的字符。 因此，`_rid` 值采用 Base64 编码。
> 
> 对于 MongoDB 集合，Azure 搜索会自动将 `_id` 属性重命名为 `doc_id`。  

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

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> “运行 API”成功返回时，已计划索引器调用，但实际处理过程以异步方式发生。 

可在门户中或使用“获取索引器状态 API”（会在接下来的内容中介绍）监视索引器状态。 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>获取索引器状态
可检索索引器的状态和执行历史记录：

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
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
数据更改检测策略旨在有效识别已更改的数据项。 目前，唯一支持的策略是 Azure Cosmos DB 提供的使用 `_ts`（时间戳）属性的 `High Water Mark` 策略，它按如下所示指定：

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

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
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
祝贺你！ 已学习了如何使用索引器将 Azure Cosmos DB 与 Azure 搜索集成。

* 若要详细了解 Azure Cosmos DB，请参阅 [Azure Cosmos DB 服务页](https://azure.microsoft.com/services/cosmos-db/)。
* 若要详细了解 Azure 搜索，请参阅[搜索服务页](https://azure.microsoft.com/services/search/)。
