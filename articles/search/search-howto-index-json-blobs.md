---
title: 使用 Azure Blob 索引器为 JSON blob 编制索引以进行全文搜索 - Azure 搜索
description: 使用 Azure 搜索 Blob 索引器抓取 Azure JSON Blob 以获取文本内容。 索引器可自动为所选数据源（如 Azure Blob 存储）引入数据。
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: cafb48f28e38794ce0757d50a5d87432b237e17c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467156"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜索 Blob 索引器为 JSON Blob 编制索引
本文介绍如何配置 Azure 搜索 Blob 索引器，以从 Azure Blob 存储中的 JSON Blob 提取结构化内容。

可以使用[门户](#json-indexer-portal)、[REST API](#json-indexer-rest) 或 [.NET SDK](#json-indexer-dotnet) 为 JSON 内容编制索引。 所有方法的共同特点是 JSON 文档位于 Azure 存储帐户中的某个 Blob 容器内。 有关从其他非 Azure 平台推送 JSON 文档的指导，请参阅 [Azure 搜索中的数据导入](search-what-is-data-import.md)。

Azure Blob 存储中的 JSON Blob 通常是单个 JSON 文档或 JSON 数组。 Azure 搜索中的 Blob 索引器可以根据请求中 **parsingMode** 参数设置方式分析上述任一构造。

> [!IMPORTANT]
> JSON blob 索引已正式发布，但 JsonArray 分析以公共预览版提供，不应在生产环境中使用。 有关详细信息，请参阅 [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md)。 

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>使用门户

为 JSON 文档编制索引的最简单方法是使用 [Azure 门户](https://portal.azure.com/)中的向导。 通过分析 Azure Blob 容器中的元数据，[**导入数据**](search-import-data-portal.md)向导可以创建默认索引、将源字段映射到目标索引字段，并以单个操作加载索引。 根据源数据的大小和复杂性，在数分钟内就能创建一个有效的全文搜索索引。

### <a name="1---prepare-source-data"></a>1 - 准备源数据

应有一个 Azure 存储帐户，其中包含 Blob 存储和 JSON 文档容器。 如果你不熟悉其中的任一任务，请查看[认知搜索快速入门](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data)中的先决条件部分“设置 Azure Blob 服务并加载示例数据”。

### <a name="2---start-import-data-wizard"></a>2 - 启动“导入数据”向导

可以通过 Azure 搜索服务页中的命令栏[启动该向导](search-import-data-portal.md)，或者在存储帐户左侧导航窗格的“Blob 服务”部分单击“添加 Azure 搜索”。

### <a name="3---set-the-data-source"></a>3 - 设置数据源

在“数据源”页中，源必须是“Azure Blob 存储”，其规范如下：

+ “要提取的数据”应是“内容和元数据”。 选择此选项可让向导推断索引架构并映射要导入的字段。
   
+ “分析模式”应设置为“JSON”或“JSON 数组”。 

  “JSON”将每个 Blob 表达为单个搜索文档，在搜索结果中作为独立的项显示。 

  “JSON 数组”适用于由多个元素组成的 Blob，你希望将其中的每个元素表达为独立的搜索文档。 如果 Blob 比较复杂，而且你未选择“JSON 数组”，则会将整个 Blob 作为单个文档引入。
   
+ “存储容器”必须指定你的存储帐户和容器，或指定解析成容器的连接字符串。 可在 Blob 服务门户页上获取连接字符串。

   ![Blob 数据源定义](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - 跳过向导中的“添加认知搜索”页

导入 JSON 文档不需要添加认知技能。 除非需要特意在索引管道中[包含认知服务 API 和转换](cognitive-search-concept-intro.md)，否则应跳过此步骤。

若要跳过该步骤，请单击下一页“自定义目标索引”。

### <a name="5---set-index-attributes"></a>5 - 设置索引属性

在“索引”页中，应会看到带有数据类型的字段列表，以及一系列用于设置索引属性的复选框。 向导可以通过源数据采样，基于元数据生成默认索引。 

默认值通常可以产生可行的解决方案：选择一个字段（强制转换为字符串）来充当用于唯一标识每个文档的键或文档 ID，并选择适合在全文搜索中使用的、可在结果集中检索的字段。 对于 Blob，`content` 字段最适合用于可搜索的内容。

可以接受默认值，或查看[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的说明，以替代或补充初始值。 

花费片刻时间来检查所做的选择。 运行向导后，将创建物理数据结构，到时，除非删除再重新创建所有对象，否则无法编辑这些字段。

   ![Blob 索引定义](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 创建索引器

完全指定设置后，向导将在搜索服务中创建三个不同的对象。 数据源对象和索引对象作为命名的资源保存在 Azure 搜索服务中。 最后一个步骤创建索引器对象。 为索引器命名可让它作为独立的资源存在，无论在同一向导序列中创建了哪种索引和数据源对象，都可以计划和管理该索引器。

如果你不熟悉索引器，请记住，索引器是 Azure 搜索中的一个资源，它可以在外部数据源中爬网，以检索可搜索的内容。 “导入数据”向导的输出是在 JSON 数据源中爬网、提取可搜索内容，然后将此内容导入 Azure 搜索中的某个索引的索引器。

   ![Blob 索引器定义](media/search-howto-index-json/import-wizard-json-indexer.png)

单击“确定”运行向导并创建所有对象。 随后会立即开始编制索引。

可以在门户页监视数据导入。 进度通知指示索引状态以及已上传的文档数。 索引编制完成后，可以使用[搜索浏览器](search-explorer.md)来查询索引。

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

为 JSON Blob 编制索引的过程类似于在 Azure 搜索的所有索引器通用的三部分工作流中提取普通文档：创建数据源、创建索引、创建索引器。

对于基于代码的 JSON 索引编制，可将 REST API 与[索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)、[数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)和[索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 API 配合使用。 与使用门户向导相比，使用代码方法需要准备好索引，并可以在发送“创建索引器”请求时接受 JSON 文档。

Azure Blob 存储中的 JSON Blob 通常是单个 JSON 文档或 JSON 数组。 Azure 搜索中的 Blob 索引器可以根据请求中 **parsingMode** 参数设置方式分析上述任一构造。

| JSON 文档 | parsingMode | 说明 | 可用性 |
|--------------|-------------|--------------|--------------|
| 每个 Blob 各有一个 | `json` | 将 JSON Blob 分析为单个文本区块。 每个 JSON Blob 成为单个 Azure 搜索文档。 | 已在 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 中正式发布。 |
| 每个 Blob 有多个 | `jsonArray` | 分析 Blob 中的 JSON 数组，其中数组的每个元素成为单独的 Azure 搜索文档。  | 已在 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 中正式发布。 |


### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

第一个步骤是提供索引器使用的数据源连接信息。 数据源类型（此处指定为 `azureblob`）确定索引器要调用哪些数据提取行为。 对于 JSON Blob 索引，数据源的定义与 JSON 文档和数组的定义相同。 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>步骤 2：创建目标搜索索引 

索引器与索引架构配对。 如果使用的是 API（而不是门户），请提前准备索引，以便可以在索引器操作中指定它。

索引在 Azure 搜索中存储可搜索的内容。 若要创建索引，请提供一个架构，用于在文档、属性和其他构造中指定可以塑造搜索体验的字段。 如果创建与源具有相同字段名称和数据类型的索引，索引器将会匹配源和目标字段，使你无需显式映射字段。

以下示例演示了一个[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)请求。 该索引包含一个可搜索的 `content` 字段，该字段存储从 Blob 提取的文本：   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>步骤 3：配置和运行索引器

到目前为止，数据源和索引的定义不识别 parsingMode。 但是，对于步骤 3 中的索引器配置，路径根据需要在 Azure 搜索中分析和构建 JSON Blob 内容的方式而有所不同。 选项包括 `json` 或 `jsonArray`：

+ 将 **parsingMode** 设置为 `json` 会将每个 Blob 作为单个文档编制索引。

+ 如果 Blob 由 JSON 数组构成，并且你需要让该数组的每个元素成为 Azure 搜索中的独立文档，请将 **parsingMode** 设置为 `jsonArray`。 可将某个文档视为搜索结果中的单个项。 如果你希望数组中的每个元素在搜索结果中显示为独立的项，请使用 `jsonArray` 选项。

在索引器定义中，可以选择性地使用**字段映射**来选择要将源 JSON 文档的哪些属性用于填充目标搜索索引。 对于 JSON 数组，如果数组作为较低级别的属性存在，则可以设置一个文档根，用于指示要将该数组放在 Blob 中的哪个位置。

> [!IMPORTANT]
> 使用 `json` 或 `jsonArray` 分析模式时，Azure 搜索假定数据源中的所有 blob 都包含 JSON。 如果需要在同一数据源中支持混合使用 JSON 和非 JSON blob，请通过[我们的 UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search)告知我们。


### <a name="how-to-parse-single-json-blobs"></a>如何分析单个 JSON Blob

默认情况下，[Azure 搜索 Blob 索引器](search-howto-indexing-azure-blob-storage.md)会将 JSON blob 分析为单个文本块。 通常会希望保留 JSON 文档的结构。 例如，假设 Azure Blob 存储中包含以下 JSON 文档：

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob 索引器将 JSON 文档分析成单个 Azure 搜索文档。 索引器通过将源中的“text”、“datePublished”和“tags”与同名且类型相同的目标索引字段进行匹配，来加载索引。

配置在索引器操作的正文中提供。 回顾前面的定义，数据源对象指定数据源类型和连接信息。 此外，目标索引也必须作为服务中的空容器存在。 计划和参数是可选的，但如果将其忽略，索引器会使用 `json` 作为分析模式立即运行。

指定了完整信息的请求可能如下所示：

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

如前所述，字段映射不是必需的。 假设某个索引包含“text”、“datePublished”和“tags”字段，则请求不需要存在字段映射，Blob 索引器就能推断正确的映射。

### <a name="how-to-parse-json-arrays"></a>如何分析 JSON 数组

或者，可以选择启用 JSON 数组功能。 当 Blob 包含 JSON 对象的数组，并且你希望每个元素都成为单独的 Azure 搜索文档时，此功能非常有用。 例如，对于以下 JSON Blob，可以使用三个单独的文档（每个文档包含“id”和“text”字段）填充 Azure 搜索。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

对于 JSON 数组，索引器定义应如以下示例所示。 请注意，parsingMode 参数指定 `jsonArray` 分析器。 指定适当的分析器和提供适当的数据输入，是为 JSON Blob 编制索引所要满足的，专门与数组相关的唯一两项要求。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

同样请注意，可以省略字段映射。 假设某个索引包含同名的“id”和“text”字段，则无需显式字段映射列表，Blob 索引器就能推断正确的映射。

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>嵌套的 JSON 数组
假设你想要对一个 JSON 对象数组编制索引，但该数组嵌套在文档的某个位置，该怎么办？ 可以使用 `documentRoot` 配置属性选取包含该数组的属性。 例如，如果 blob 如下所示：

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

使用此配置可为 `level2` 属性中包含的数组编制索引：

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

### <a name="using-field-mappings-to-build-search-documents"></a>使用字段映射生成搜索文档

当源与目标字段未完美对齐时，可以在请求正文中定义一个字段映射节，以建立显式的字段间关联。

目前，Azure 搜索不能直接索引任意 JSON 文档，因为它只支持基元数据类型、字符串数组和 GeoJSON 点。 不过，可以使用**字段映射**选取 JSON 文档的部分，并将它们“提升”到搜索文档的顶级字段。 若要了解字段映射的基础知识，请参阅 [Azure 搜索索引器中的字段映射](search-indexer-field-mappings.md)。

回到前面的示例 JSON 文档：

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

假设某个搜索索引包含以下字段：`Edm.String` 类型的 `text`、`Edm.DateTimeOffset` 类型的 `date`，以及 `Collection(Edm.String)` 类型的 `tags`。 请注意源中“datePublished”与索引中 `date` 字段之间的差异。 要将 JSON 映射到所需形状，请使用以下字段映射：

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

使用 [JSON 指针](https://tools.ietf.org/html/rfc6901)表示法指定映射中的源字段名称。 以正斜杠开头引用 JSON 文档的根，并通过使用正斜杠分隔的路径选取所需属性（任意层级的嵌套）。

还可以通过使用从零开始的索引来引用个别数组元素。 例如，若要选取上述示例中“tags”数组的第一个元素，请使用如下所示的字段映射：

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 如果字段映射路径中的源字段名称引用了 JSON 中不存在的属性，则会跳过该映射，不会出错。 如此，我们便可以支持具有不同架构的文档（这是一个常见用例）。 因为没有任何验证，所以需要注意避免字段映射规范中出现拼写错误。
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>REST 示例：包含字段映射的索引器请求

以下示例是一个指定了完整信息的索引器有效负载，其中包括字段映射：

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>使用 .NET SDK

.NET SDK 完全可与 REST API 搭配使用。 我们建议查看前面的 REST API 部分，以了解相关概念、工作流和要求。 然后，可以参阅以下 .NET API 参考文档，在托管代码中实现 JSON 索引器。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>另请参阅

+ [Azure 搜索中的索引器](search-indexer-overview.md)
+ [使用 Azure 搜索为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)
+ [使用 Azure 搜索 Blob 索引器为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)
+ [教程：在 Azure Blob 存储中搜索半结构化数据](search-semi-structured-data.md)
