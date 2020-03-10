---
title: 搜索 Azure Cosmos DB 的数据
titleSuffix: Azure Cognitive Search
description: 将 Azure Cosmos DB 中的数据导入 Azure 认知搜索中的可搜索索引。 索引器可自动为所选数据源（如 Azure Cosmos DB）引入数据。
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391250"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>如何在 Azure 中使用索引器对 Cosmos DB 数据编制索引认知搜索 

> [!IMPORTANT] 
> SQL API 已正式发布。
> MongoDB API、Gremlin API 和 Cassandra API 支持目前为公共预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 你可以通过填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)来请求对预览的访问权限。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

> [!WARNING]
> Azure 认知搜索仅支持将[索引策略](https://docs.microsoft.com/azure/cosmos-db/index-policy)设置为[一致](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)的 Cosmos DB 集合。 不建议使用延迟索引策略对集合编制索引，这可能会导致数据丢失。 不支持禁用索引的集合。

本文介绍如何配置 Azure Cosmos DB[索引器](search-indexer-overview.md)来提取内容并使其在 Azure 认知搜索中可搜索。 此工作流创建 Azure 认知搜索索引，并使用从 Azure Cosmos DB 中提取的现有文本加载该索引。 

由于术语可能令人困惑，值得注意的是， [Azure Cosmos DB 索引](https://docs.microsoft.com/azure/cosmos-db/index-overview)和[Azure 认知搜索索引](search-what-is-an-index.md)是不同的操作，每个服务都是唯一的。 开始 Azure 认知搜索编制索引之前，Azure Cosmos DB 数据库必须已存在并且包含数据。

Azure 认知搜索中的 Cosmos DB 索引器可对通过不同协议访问[Azure Cosmos DB 项](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items)进行爬网。 

+ 对于通常可用的[SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference)，可以使用[门户](#cosmos-indexer-portal)、 [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)或[.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)来创建数据源和索引器。

+ 对于[MONGODB API （预览版）](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)，可以使用[门户](#cosmos-indexer-portal)或[REST API 版本 2019-05-06-preview](search-api-preview.md)来创建数据源和索引器。

+ 对于[Cassandra API （预览版）](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)和[Gremlin API （预览版）](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)，只能使用[REST API 版本 2019-05-06-preview](search-api-preview.md)来创建数据源和索引器。


> [!Note]
> 如果要在 Azure 认知搜索中查看受支持的[表 API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) ，则可以为该转换用户语音的投票。
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>使用门户

> [!Note]
> 门户当前支持 SQL API 和 MongoDB API （预览）。

索引 Azure Cosmos DB 项的最简单方法是使用[Azure 门户](https://portal.azure.com/)中的向导。 通过对容器中的数据进行采样和读取元数据，Azure 认知搜索中的 "[**导入数据**](search-import-data-portal.md)" 向导可以创建一个默认索引，将源字段映射到目标索引字段，并在单个操作中加载索引。 根据源数据的大小和复杂性，在数分钟内就能创建一个有效的全文搜索索引。

建议为 Azure 认知搜索和 Azure Cosmos DB 使用同一区域或位置，以降低延迟并避免带宽费用。

### <a name="1---prepare-source-data"></a>1 - 准备源数据

应该有一个 Cosmos DB 帐户、一个映射到 SQL API 的 Azure Cosmos DB 数据库、MongoDB API （预览）或 Gremlin API （预览），以及数据库中的内容。

请确保 Cosmos DB 数据库包含数据。 [导入数据向导](search-import-data-portal.md)可读取元数据并执行数据采样，以推断索引架构，但它也会从 Cosmos DB 加载数据。 如果缺少数据，向导将停止并出现以下错误： "从数据源检测索引架构：错误：无法生成原型索引，因为数据源 ' emptycollection ' 未返回数据"。

### <a name="2---start-import-data-wizard"></a>2 - 启动“导入数据”向导

你可以从 "Azure 认知搜索服务" 页的命令栏中[启动该向导](search-import-data-portal.md)，或者，如果要连接到 COSMOS DB SQL API，你可以在 Cosmos DB 帐户的左侧导航窗格的 "**设置**" 部分中单击 "**添加 Azure 认知搜索**"。

   ![门户中的 "导入数据" 命令](./media/search-import-data-portal/import-data-cmd2.png "启动“导入数据”向导")

### <a name="3---set-the-data-source"></a>3 - 设置数据源

在 "**数据源**" 页中，源必须**Cosmos DB**，并具有以下规范：

+ **Name**是数据源对象的名称。 创建后，您可以为其他工作负荷选择该方法。

+ **Cosmos DB 帐户**应为来自 Cosmos DB 的主或辅助连接字符串，`AccountEndpoint` 和 `AccountKey`。 对于 MongoDB 集合，请将 **/apikind/= MongoDB**添加到连接字符串的末尾，并使用分号将其与连接字符串隔开。 对于 Gremlin API 和 Cassandra API，请使用[REST API](#cosmosdb-indexer-rest)的说明。

+ **数据库**是该帐户的现有数据库。 

+ **集合**是文档的容器。 文档必须存在才能成功导入。 

+ 如果您想要所有文档，则**查询**可以为空，否则您可以输入一个选择文档子集的查询。 **查询**仅可用于 SQL API。

   ![Cosmos DB 数据源定义](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB 数据源定义")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-跳过向导中的 "充实内容" 页面

添加认知技能（或扩充）不是一种导入需求。 除非有特定需要[将 AI 扩充添加](cognitive-search-concept-intro.md)到索引管道，否则应跳过此步骤。

若要跳过该步骤，请单击页面底部的蓝色按钮 "下一步" 和 "跳过"。

### <a name="5---set-index-attributes"></a>5 - 设置索引属性

在“索引”页中，应会看到带有数据类型的字段列表，以及一系列用于设置索引属性的复选框。 此向导可以基于元数据生成字段列表，并通过采样源数据。 

通过单击属性列顶部的复选框，可以批量选择属性。 对于应返回到客户端应用并受全文搜索处理的每个字段，选择 "可**检索**和可**搜索**"。 您会注意到整数不是全文或模糊搜索（数字按原义计算，在筛选器中通常很有用）。

有关详细信息，请查看[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的说明。 

花费片刻时间来检查所做的选择。 运行向导后，将创建物理数据结构，到时，除非删除再重新创建所有对象，否则无法编辑这些字段。

   ![Cosmos DB 索引定义](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB 索引定义")

### <a name="6---create-indexer"></a>6 - 创建索引器

完全指定设置后，向导将在搜索服务中创建三个不同的对象。 数据源对象和索引对象保存为 Azure 认知搜索服务中的已命名资源。 最后一个步骤创建索引器对象。 为索引器命名可让它作为独立的资源存在，无论在同一向导序列中创建了哪种索引和数据源对象，都可以计划和管理该索引器。

如果你不熟悉索引器，*索引器*是 Azure 认知搜索中的资源，用于对外部数据源进行爬网搜索。 **导入数据**向导的输出是一个索引器，该索引器对您的 Cosmos DB 数据源进行爬网，提取可搜索的内容，然后将其导入 Azure 认知搜索的索引。

以下屏幕截图显示默认索引器配置。 如果希望一次运行索引器，则可以切换到 "**一次**"。 单击 "**提交**" 以运行该向导并创建所有对象。 随后会立即开始编制索引。

   ![Cosmos DB 索引器定义](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB 索引器定义")

可以在门户页监视数据导入。 进度通知指示索引状态以及已上传的文档数。 

索引编制完成后，可以使用[搜索浏览器](search-explorer.md)来查询索引。

> [!NOTE]
> 如果看不到预期的数据，则可能需要对更多字段设置更多属性。 删除你刚创建的索引和索引器，并再次逐句通过向导，同时修改步骤5中索引属性所做的选择。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

可以使用 REST API 来索引 Azure Cosmos DB 数据，并遵循 Azure 认知搜索中所有索引器共有的三部分工作流：创建数据源、创建索引、创建索引器。 提交 Create 索引器请求时，将发生从 Cosmos DB 的数据提取。 完成此请求后，将具有可查询的索引。 

> [!NOTE]
> 若要为来自 Cosmos DB Gremlin API 或 Cosmos DB Cassandra API 的数据编制索引，必须先通过填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)请求访问封闭预览。 处理请求后，将收到有关如何使用[REST API 版本 2019-05-06-Preview](search-api-preview.md)来创建数据源的说明。

本文前面介绍[Azure Cosmos DB 索引](https://docs.microsoft.com/azure/cosmos-db/index-overview)编制和[Azure 认知搜索索引](search-what-is-an-index.md)编制索引是不同的操作。 对于 Cosmos DB 索引，默认情况下会自动为所有文档编制索引，除非 Cassandra API。 如果关闭自动索引编制，则只能通过其自链接或通过使用文档 ID 进行查询来访问文档。 Azure 认知搜索索引需要在将由 Azure 认知搜索编制索引的集合中打开 Cosmos DB 自动索引。 注册 Cosmos DB Cassandra API 索引器预览时，将向你提供有关如何设置 Cosmos DB 索引的说明。

> [!WARNING]
> Azure Cosmos DB 是下一代 DocumentDB。 以前使用 API 版本**2017-11-11** ，你可以使用 `documentdb` 语法。 这意味着您可以将数据源类型指定为 `cosmosdb` 或 `documentdb`。 从 API 版本**2019-05-06**开始，Azure 认知搜索 Api 和门户仅支持本文中所述的 `cosmosdb` 语法。 这意味着，如果想要连接到 Cosmos DB 终结点，数据源类型必须 `cosmosdb`。

### <a name="1---assemble-inputs-for-the-request"></a>1-汇集请求的输入

对于每个请求，必须提供 Azure 认知搜索的服务名称和管理密钥（在 POST 标头中）以及 blob 存储的存储帐户名称和密钥。 可以使用[Postman](search-get-started-postman.md)将 HTTP 请求发送到 Azure 认知搜索。

将以下四个值复制到记事本中，以便可以将它们粘贴到请求中：

+ Azure 认知搜索服务名称
+ Azure 认知搜索管理密钥
+ Cosmos DB 连接字符串

可以在门户中找到以下值：

1. 在 Azure 认知搜索的门户页中，从 "概述" 页复制 "搜索服务 URL"。

2. 在左侧导航窗格中，单击 "**密钥**"，然后复制 "主密钥" 或 "辅助密钥" （它们等效）。

3. 切换到 Cosmos 存储帐户的门户页面。 在左侧导航窗格中的 "**设置**" 下，单击 "**密钥**"。 此页提供 URI、两组连接字符串和两组键。 将其中一个连接字符串复制到记事本。

### <a name="2---create-a-data-source"></a>2-创建数据源

**数据源**指定要编制索引的数据、凭据和用于识别数据更改（如修改或删除了集合内的文档）的策略。 数据源定义为独立的资源，以便它可以被多个索引器使用。

若要创建数据源，请构建 POST 请求：

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
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

| 字段   | 说明 |
|---------|-------------|
| name | 必需。 选择任意名称来表示数据源对象。 |
|type| 必需。 必须是 `cosmosdb`。 |
|**凭据** | 必需。 必须是 Cosmos DB 的连接字符串。<br/>对于 SQL 集合，连接字符串采用以下格式： `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>对于 MongoDB 集合，请将 **/apikind/= MongoDB**添加到连接字符串：<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>对于 Gremlin 关系图和 Cassandra 表，注册 "[封闭索引器预览](https://aka.ms/azure-cognitive-search/indexer-preview)" 以获取对预览版的访问权限，以及有关如何设置凭据格式的信息。<br/><br/>避免在终结点 URL 中包含端口号。 如果包含端口号，Azure 认知搜索将无法为你的 Azure Cosmos DB 数据库编制索引。|
| **容器** | 包含下列元素： <br/>**名称**：必需。 指定要编制索引的数据库集合的 ID。<br/>**查询**：可选。 可以指定查询，将任意 JSON 文档平展成 Azure 认知搜索可以为其编制索引的平面架构。<br/>对于 MongoDB API、Gremlin API 和 Cassandra API，不支持查询。 |
| **dataChangeDetectionPolicy** | 推荐。 请参阅[为已更改的文档编制索引](#DataChangeDetectionPolicy)部分。|
|**dataDeletionDetectionPolicy** | 可选。 请参阅[为已删除的文档编制索引](#DataDeletionDetectionPolicy)部分。|

### <a name="using-queries-to-shape-indexed-data"></a>使用查询形成索引数据
可以指定一个 SQL 查询来平展嵌套的属性或数组、投影 JSON 属性并筛选要编制索引的数据。 

> [!WARNING]
> **MONGODB api**、 **Gremlin api**和**Cassandra API**不支持自定义查询： `container.query` 参数必须设置为 null 或省略。 如果需要使用自定义查询，请在[用户之声](https://feedback.azure.com/forums/263029-azure-search)上告知我们。

示例文档：

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

筛选查询：

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

平展查询：

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
投影查询：

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


数组平展查询：

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3-创建目标搜索索引 

如果还没有[目标 Azure 认知搜索索引](/rest/api/searchservice/create-index)，请创建一个。 下面的示例创建具有 ID 和说明字段的索引：

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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
> 对于分区集合，默认文档键是 Azure Cosmos DB 的 `_rid` 属性，Azure 认知搜索会自动将其重命名为 `rid`，因为字段名称不能以下划线字符开头。 此外，Azure Cosmos DB `_rid` 值包含在 Azure 认知搜索密钥中无效的字符。 因此，`_rid` 值采用 Base64 编码。
> 
> 对于 MongoDB 集合，Azure 认知搜索会自动将 `_id` 属性重命名为 "`id`"。  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON 数据类型与 Azure 认知搜索数据类型之间的映射
| JSON 数据类型 | 兼容的目标索引字段类型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 类似于整数的数字 |Edm.Int32、Edm.Int64、Edm.String |
| 类似于浮点的数字 |Edm.Double、Edm.String |
| String |Edm.String |
| 基元类型的数组，如 ["a", "b", "c"] |集合 (Edm.String) |
| 类似于日期的字符串 |Edm.DateTimeOffset、Edm.String |
| GeoJSON 对象，如 { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 其他 JSON 对象 |空值 |

### <a name="4---configure-and-run-the-indexer"></a>4-配置并运行索引器

创建索引和数据源后，就可以准备创建索引器了：

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

此索引器每两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

有关定义索引器计划的详细信息，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

## <a name="use-net"></a>使用 .NET

已公开发布的 .NET SDK 与公开可用的 REST API 完全相同。 我们建议查看前面的 REST API 部分，以了解相关概念、工作流和要求。 然后，可以参阅以下 .NET API 参考文档，在托管代码中实现 JSON 索引器。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>为已更改的文档编制索引

数据更改检测策略旨在有效识别已更改的数据项。 目前，唯一受支持的策略是使用 Azure Cosmos DB 提供的 `_ts` （时间戳）属性的[`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) ，如下所示：

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

强烈建议使用此策略，以确保索引器性能良好。 

如果使用自定义查询，请确保查询投影 `_ts` 属性。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>增量操作和自定义查询

索引编制过程中的增量操作可确保由于暂时性故障或执行时间限制而中断索引器执行时，索引器能够在下次运行时从中断位置运行，而不是从头开始重新为整个集合编制索引。 在为大型集合编制索引时，这一点尤其重要。 

要在使用自定义查询时启用增量操作，请确保查询按照 `_ts` 列对结果进行排序。 这会启用定期检查，Azure 认知搜索使用此功能在出现故障时提供增量进度。   

在某些情况下，即使您的查询中包含 `ORDER BY [collection alias]._ts` 子句，Azure 认知搜索也可能不会推断该查询按 `_ts`排序。 可以通过使用 `assumeOrderByHighWaterMarkColumn` 配置属性，告知 Azure 认知搜索对结果进行排序。 要指定此提示，请按如下所示创建或更新索引器： 

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

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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

祝贺你！ 已了解如何使用索引器将 Azure Cosmos DB 与 Azure 认知搜索集成。

* 若要详细了解 Azure Cosmos DB，请参阅 [Azure Cosmos DB 服务页](https://azure.microsoft.com/services/cosmos-db/)。
* 若要了解有关 Azure 认知搜索的详细信息，请参阅[搜索服务页](https://azure.microsoft.com/services/search/)。
