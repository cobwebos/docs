---
title: 使用 Azure 搜索 Blob 索引器对 JSON blob 编制索引
description: 使用 Azure 搜索 Blob 索引器对 JSON blob 编制索引
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 5b4cd1c592c4cd965a0b5d9e4fb8eef84a6bea91
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003275"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜索 Blob 索引器对 JSON blob 编制索引
本文介绍如何配置 Azure 搜索 Blob 索引器，以从 Azure Blob 存储中的 JSON Blob 提取结构化内容。

Azure Blob 存储中的 JSON Blob 通常是单个 JSON 文档或 JSON 数组。 Azure 搜索中的 Blob 索引器可以根据请求中 **parsingMode** 参数设置方式分析上述任一构造。

| JSON 文档 | parsingMode | Description | 可用性 |
|--------------|-------------|--------------|--------------|
| 每个 Blob 各有一个 | `json` | 将 JSON Blob 分析为单个文本区块。 每个 JSON Blob 成为单个 Azure 搜索文档。 | 已在 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API 中正式发布。 |
| 每个 Blob 有多个 | `jsonArray` | 分析 Blob 中的 JSON 数组，其中数组的每个元素成为单独的 Azure 搜索文档。  | 在预览版中，采用 [REST api-version=`2017-11-11-Preview`](search-api-2017-11-11-preview.md) 和 [.NET SDK 预览](https://aka.ms/search-sdk-preview)格式。 |

> [!Note]
> 预览 API 用于测试和评估，不应在生产环境中使用。
>

## <a name="setting-up-json-indexing"></a>设置 JSON 索引
为 JSON Blob 编制索引的过程类似于在 Azure 搜索的所有索引器通用的三部分工作流中提取普通文档。

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

> [!Note]
> 有限数量的正式版索引器在门户中通过“导入”操作公开。 通常，导入工作流可以基于源中的元数据构造初步索引。 有关详细信息，请参阅[在门户中将数据导入 Azure 搜索](search-import-data-portal.md)。

### <a name="step-3-configure-and-run-the-indexer"></a>步骤 3：配置和运行索引器

到目前为止，数据源和索引的定义不识别 parsingMode。 但是，对于步骤 3 中的索引器配置，路径根据需要在 Azure 搜索中分析和构建 JSON Blob 内容的方式而有所不同。

调用索引器时，请执行以下操作：

+ 将 **parsingMode** 参数设置为 `json`（将每个 Blob 作为单个文档进行索引编制）或 `jsonArray`（如果 Blob 包含 JSON 数组，且需要将数组的每个元素视为单独的文档）。

+ （可选）使用**字段映射**选择要将源 JSON 文档的哪些属性用于填充目标搜索索引。 对于 JSON 数组，如果数组作为较低级别的属性存在，则可以设置一个文档根，用于指示要将该数组放在 Blob 中的哪个位置。

> [!IMPORTANT]
> 使用 `json` 或 `jsonArray` 分析模式时，Azure 搜索假定数据源中的所有 blob 都包含 JSON。 如果需要在同一数据源中支持混合使用 JSON 和非 JSON blob，请通过[我们的 UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search)告知我们。


## <a name="how-to-parse-single-json-blobs"></a>如何分析单个 JSON Blob

默认情况下，[Azure 搜索 Blob 索引器](search-howto-indexing-azure-blob-storage.md)会将 JSON blob 分析为单个文本块。 通常会希望保留 JSON 文档的结构。 例如，假设 Azure Blob 存储中包含以下 JSON 文档：

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>单个 JSON Blob 的索引器定义

使用 Azure 搜索 Blob 索引器时，如前面示例中所示的 JSON 文档将分析成单个 Azure 搜索文档。 索引器通过将源中的“text”、“datePublished”和“tags”与同名且类型相同的目标字段进行匹配，来加载索引。

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

## <a name="how-to-parse-json-arrays-preview"></a>如何分析 JSON 数组（预览）

或者，可以选择启用 JSON 数组预览功能。 当 Blob 包含 JSON 对象的数组，并且你希望每个元素都成为单独的 Azure 搜索文档时，此功能非常有用。 例如，对于以下 JSON Blob，可以使用三个单独的文档（每个文档包含“id”和“text”字段）填充 Azure 搜索。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>JSON 数组的索引器定义

对于 JSON 数组，索引器请求将会使用预览 API 和 `jsonArray` 分析器。 为 JSON Blob 编制索引时，只需满足与数组相关的这两项要求。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

同样请注意，字段映射不是必需的。 假设某个索引包含“id”和“text”字段，则不需要字段映射列表，Blob 索引器就能推断正确的映射。

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

## <a name="using-field-mappings-to-build-search-documents"></a>使用字段映射生成搜索文档

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

使用 [JSON 指针](http://tools.ietf.org/html/rfc6901)表示法指定映射中的源字段名称。 以正斜杠开头引用 JSON 文档的根，并通过使用正斜杠分隔的路径选取所需属性（任意层级的嵌套）。

还可以通过使用从零开始的索引来引用个别数组元素。 例如，若要选取上述示例中“tags”数组的第一个元素，请使用如下所示的字段映射：

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 如果字段映射路径中的源字段名称引用了 JSON 中不存在的属性，则会跳过该映射，不会出错。 如此，我们便可以支持具有不同架构的文档（这是一个常见用例）。 因为没有任何验证，所以需要注意避免字段映射规范中出现拼写错误。
>
>

## <a name="example-indexer-request-with-field-mappings"></a>示例：包含字段映射的索引器请求

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


## <a name="help-us-make-azure-search-better"></a>帮助我们改进 Azure 搜索
如果有功能请求或改进建议，请通过我们的 [UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search/)与我们联系。

## <a name="see-also"></a>另请参阅

+ [Azure 搜索中的索引器](search-indexer-overview.md)
+ [使用 Azure 搜索为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)
+ [使用 Azure 搜索 Blob 索引器为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)
+ [教程：在 Azure Blob 存储中搜索半结构化数据](search-semi-structured-data.md)
