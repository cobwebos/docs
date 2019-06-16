---
title: 使用 Azure Blob 索引器为 JSON blob 编制索引以进行全文搜索 - Azure 搜索
description: 使用 Azure 搜索 Blob 索引器抓取 Azure JSON Blob 以获取文本内容。 索引器可自动为所选数据源（如 Azure Blob 存储）引入数据。
ms.date: 05/02/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4f3fb624f5e6137c9edb0be97adc16d8c808ebd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523077"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>如何使用 Azure 搜索 Blob 索引器为 JSON Blob 编制索引
本文介绍如何配置 Azure 搜索 Blob [索引器](search-indexer-overview.md)，以从 Azure Blob 存储中的 JSON 文档提取结构化内容，并使其在 Azure 搜索中可供搜索。 此工作流将创建一个 Azure 搜索索引，然后连同从 JSON Blob 中提取的现有文本一起加载该索引。 

可以使用[门户](#json-indexer-portal)、[REST API](#json-indexer-rest) 或 [.NET SDK](#json-indexer-dotnet) 为 JSON 内容编制索引。 所有方法的共同特点是 JSON 文档位于 Azure 存储帐户中的某个 Blob 容器内。 有关从其他非 Azure 平台推送 JSON 文档的指导，请参阅 [Azure 搜索中的数据导入](search-what-is-data-import.md)。

Azure Blob 存储中的 JSON Blob 通常是单个 JSON 文档或 JSON 实体集合。 对于 JSON 集合，Blob 可以包含正确格式的 JSON 元素的**数组**。 Blob 还可以包含由换行符分隔的多个 JSON 实体。 Azure 搜索中的 Blob 索引器可以根据请求中的 **parsingMode** 参数设置方式分析任何此类构造。

所有 JSON 分析模式（`json`、`jsonArray`、`jsonLines`）现已推出正式版。 

> [!NOTE]
> 请遵循[一对多索引](search-howto-index-one-to-many-blobs.md)中的索引器配置建议从一个 Azure Blob 输出多个搜索文档。

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>使用门户

为 JSON 文档编制索引的最简单方法是使用 [Azure 门户](https://portal.azure.com/)中的向导。 通过分析 Azure Blob 容器中的元数据，[**导入数据**](search-import-data-portal.md)向导可以创建默认索引、将源字段映射到目标索引字段，并以单个操作加载索引。 根据源数据的大小和复杂性，在数分钟内就能创建一个有效的全文搜索索引。

我们建议对 Azure 搜索和 Azure 存储使用同一个 Azure 订阅，并且两者最好是在同一个区域。

### <a name="1---prepare-source-data"></a>1 - 准备源数据

1. [登录到 Azure 门户](https://portal.azure.com/)。

1. [创建一个 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)用于包含数据。 可将“公共访问级别”设为任何有效值。

在“导入数据”向导中检索数据时，需要用到存储帐户名称、容器名称和访问密钥。 

### <a name="2---start-import-data-wizard"></a>2 - 启动“导入数据”向导

在 Azure 搜索服务的“概述”页中，可以通过命令栏[启动该向导](search-import-data-portal.md)，或者在存储帐户左侧导航窗格的“Blob 服务”部分单击“添加 Azure 搜索”。  

   ![门户中的导入数据命令](./media/search-import-data-portal/import-data-cmd2.png "启动导入数据向导")

### <a name="3---set-the-data-source"></a>3 - 设置数据源

在“数据源”页中，源必须是“Azure Blob 存储”，其规范如下：  

+ “要提取的数据”应是“内容和元数据”。   选择此选项可让向导推断索引架构并映射要导入的字段。
   
+ “分析模式”应设置为“JSON”、“JSON 数组”或“JSON 行”。     

  “JSON”将每个 Blob 表达为单个搜索文档，在搜索结果中作为独立的项显示。  

  “JSON 数组”适用于包含正确格式的 JSON 数据的 Blob - 正确格式的 JSON 对应于某个对象数组或包含某个属性（一个对象数组），你希望将每个元素表达为独立的搜索文档。  如果 Blob 比较复杂，而且你未选择“JSON 数组”，则会将整个 Blob 作为单个文档引入。 

  “JSON 行”适用于包含多个以换行符分隔的 JSON 实体的 Blob，你希望将其中的每个实体表达为独立的搜索文档。  如果 Blob 比较复杂，而你未选择“JSON 行”分析模式，则会将整个 Blob 作为单个文档引入。 
   
+ “存储容器”必须指定你的存储帐户和容器，或指定解析成容器的连接字符串。  可在 Blob 服务门户页上获取连接字符串。

   ![Blob 数据源定义](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - 跳过向导中的“添加认知搜索”页

导入 JSON 文档不需要添加认知技能。 除非需要特意在索引管道中[包含认知服务 API 和转换](cognitive-search-concept-intro.md)，否则应跳过此步骤。

若要跳过该步骤，请先转到下一页。

   ![认知搜索的下一页按钮](media/search-get-started-portal/next-button-add-cog-search.png)

在该页中，可以直接跳到索引自定义操作。

   ![跳过认知技能步骤](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - 设置索引属性

在“索引”页中，应会看到带有数据类型的字段列表，以及一系列用于设置索引属性的复选框。  向导可以通过源数据采样，基于元数据生成字段列表。 

可以通过单击属性列顶部的复选框，来批量选择属性。 对于应该返回给客户端应用并且需要接受全文搜索处理的每个字段，请选择“可检索”和“可搜索”。   你会注意到，无法对整数进行全文搜索或模糊搜索（数字按原义评估，通常在筛选器中使用）。

有关详细信息，请查看[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的说明。 

花费片刻时间来检查所做的选择。 运行向导后，将创建物理数据结构，到时，除非删除再重新创建所有对象，否则无法编辑这些字段。

   ![Blob 索引定义](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 创建索引器

完全指定设置后，向导将在搜索服务中创建三个不同的对象。 数据源对象和索引对象作为命名的资源保存在 Azure 搜索服务中。 最后一个步骤创建索引器对象。 为索引器命名可让它作为独立的资源存在，无论在同一向导序列中创建了哪种索引和数据源对象，都可以计划和管理该索引器。

如果你不熟悉索引器，请记住，索引器是 Azure 搜索中的一个资源，它可以在外部数据源中爬网，以检索可搜索的内容。  “导入数据”向导的输出是在 JSON 数据源中爬网、提取可搜索内容，然后将此内容导入 Azure 搜索中的某个索引的索引器。 

   ![Blob 索引器定义](media/search-howto-index-json/import-wizard-json-indexer.png)

单击“确定”运行向导并创建所有对象。  随后会立即开始编制索引。

可以在门户页监视数据导入。 进度通知指示索引状态以及已上传的文档数。 

索引编制完成后，可以使用[搜索浏览器](search-explorer.md)来查询索引。

> [!NOTE]
> 如果未看到所需的数据，可能需要在更多字段中设置更多的属性。 删除刚刚创建的索引和索引器，再次完成向导中的每个步骤，并修改在步骤 5 中对索引属性所做的选择。 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

可以遵循 Azure 搜索中所有索引器通用的三部分工作流，使用 REST API 为 JSON Blob 编制索引：创建数据源、创建索引、创建索引器。 提交“创建索引器”请求时，将从 Blob 存储提取数据。 完成此请求后，将获得一个可查询的索引。 

可以查看本部分末尾的 [REST 示例代码](#rest-example)，其中演示了如何创建所有三个对象。 本部分还包含有关 [JSON 分析模式](#parsing-modes)、[单一 Blob](#parsing-single-blobs)、[JSON 数组](#parsing-arrays)和[嵌套数组](#nested-json-arrays)的详细信息。

对于基于代码的 JSON 索引编制，请使用 [Postman](search-fiddler.md) 和 REST API 创建这些对象：

+ [索引](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

操作顺序要求按此顺序创建并调用对象。 与门户工作流相比，基于代码的方法要求通过一个可用的索引来接受通过“创建索引器”请求发送的 JSON 文档。 

Azure Blob 存储中的 JSON Blob 通常是单个 JSON 文档或 JSON“数组”。 Azure 搜索中的 Blob 索引器可以根据请求中 **parsingMode** 参数设置方式分析上述任一构造。

| JSON 文档 | parsingMode | 描述 | 可用性 |
|--------------|-------------|--------------|--------------|
| 每个 Blob 各有一个 | `json` | 将 JSON Blob 分析为单个文本区块。 每个 JSON Blob 成为单个 Azure 搜索文档。 | [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 均已推出正式版。 |
| 每个 Blob 有多个 | `jsonArray` | 分析 Blob 中的 JSON 数组，其中数组的每个元素成为单独的 Azure 搜索文档。  | [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 均已推出正式版。 |
| 每个 Blob 有多个 | `jsonLines` | 分析包含换行符分隔的多个 JSON 实体（“数组”）的 Blob，其中的每个实体将成为独立的 Azure 搜索文档。 | [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 均已推出正式版。 |

### <a name="1---assemble-inputs-for-the-request"></a>1 - 汇编请求的输入

对于每个请求，必须提供 Azure 搜索的服务名称和管理密钥（在 POST 标头中），以及 Blob 存储的存储帐户名称和密钥。 可以使用 [Postman](search-fiddler.md) 将 HTTP 请求发送到 Azure 搜索。

将以下四个值复制到记事本中，以便将其粘贴到请求：

+ Azure 搜索服务名称
+ Azure 搜索管理密钥
+ Azure 存储帐户名称
+ Azure 存储帐户密钥

可以在门户中找到这些值：

1. 在 Azure 搜索的门户页中，从“概述”页复制搜索服务 URL。

2. 在左侧导航窗格中单击“密钥”，然后复制主密钥或辅助密钥（两者是等效的）。 

3. 切换到存储帐户的门户页。 在左侧导航窗格中的“设置”下，单击“访问密钥”。   此页提供帐户名称和密钥。 将存储帐户名称和其中一个密钥复制到记事本。

### <a name="2---create-a-data-source"></a>2 - 创建数据源

此步骤提供索引器使用的数据源连接信息。 数据源是 Azure 搜索中用于保存连接信息的命名对象。 数据源类型 `azureblob` 确定索引器要调用哪些数据提取行为。 

请将服务名称、管理密钥、存储帐户和帐户密钥占位符替换为有效值。

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - 创建目标搜索索引 

索引器与索引架构配对。 如果使用的是 API（而不是门户），请提前准备索引，以便可以在索引器操作中指定它。

索引在 Azure 搜索中存储可搜索的内容。 若要创建索引，请提供一个架构，用于在文档、属性和其他构造中指定可以塑造搜索体验的字段。 如果创建与源具有相同字段名称和数据类型的索引，索引器将会匹配源和目标字段，使你无需显式映射字段。

以下示例演示了一个[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)请求。 该索引包含一个可搜索的 `content` 字段，该字段存储从 Blob 提取的文本：   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - 配置并运行索引器

与索引和数据源一样，索引器也是你创建的、可在 Azure 搜索服务中重复使用的命名对象。 完全指定的用于创建索引器的请求可能如下所示：

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

索引器配置位于请求的正文中。 它要求提供一个数据源，以及已在 Azure 搜索中存在的一个空目标索引。 

schedule 和 parameters 是可选的。 如果将其省略，索引器将使用 `json` 作为分析模式立即运行。

此特定索引器不包含字段映射。 在索引器定义中，如果源 JSON 文档的属性与目标搜索索引的字段相匹配，则可以省略**字段映射**。 


### <a name="rest-example"></a>REST 示例

本部分回顾用于创建对象的所有请求。 有关组成部分的介绍，请参阅本文前面的部分。

### <a name="data-source-request"></a>数据源请求

所有索引器都需要一个提供现有数据连接信息的数据源对象。 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>索引请求

所有索引器都需要一个接收数据的目标索引。 请求的正文定义索引架构，该架构由字段构成，帮助支持可搜索索引中的所需行为。 运行索引器时，此索引应该是空的。 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>索引器请求

此请求显示完全指定的索引器。 它包含前面示例中已省略的字段映射。 回顾前文，“schedule”、“parameters”和“fieldMappings”都是可选的，只要存在可用的默认值即可。 省略“schedule”会导致索引器立即运行。 省略“parsingMode”会导致索引使用“json”默认值。

在 Azure 搜索中创建索引器会触发数据导入。 索引器会立即运行，然后按计划运行（如果提供了计划）。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>分析模式

JSON Blob 可以采用多个表单。 JSON 索引器中的 **parsingMode** 参数确定如何在 Azure 搜索索引中分析和结构化 JSON Blob 内容：

| parsingMode | 描述 |
|-------------|-------------|
| `json`  | 将每个 Blob 作为单个文档编制索引。 这是默认值。 |
| `jsonArray` | 如果 Blob 由 JSON 数组构成，并且你需要让该数组的每个元素成为 Azure 搜索中的独立文档，请选择此模式。 |
|`jsonLines` | 如果 Blob 由多个以的换行符分隔的 JSON 实体构成，并且你需要让每个实体成为 Azure 搜索中的独立文档，请选择此模式。 |

可将某个文档视为搜索结果中的单个项。 如果你希望数组中的每个元素在搜索结果中显示为独立的项，请相应地使用 `jsonArray` 或 `jsonLines` 选项。

在索引器定义中，可以选择性地使用[字段映射](search-indexer-field-mappings.md)来选择要将源 JSON 文档的哪些属性用于填充目标搜索索引。 对于 `jsonArray` 分析模式，如果数组作为较低级别的属性存在，则可以设置一个文档根，用于指示要将该数组放在 Blob 中的哪个位置。

> [!IMPORTANT]
> 当你使用`json`，`jsonArray`或`jsonLines`分析模式下，Azure 搜索假定数据源中的所有 blob 都包含 JSON。 如果需要在同一数据源中支持混合使用 JSON 和非 JSON blob，请通过[我们的 UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search)告知我们。


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>分析单个 JSON Blob

默认情况下，[Azure 搜索 Blob 索引器](search-howto-indexing-azure-blob-storage.md)会将 JSON blob 分析为单个文本块。 通常会希望保留 JSON 文档的结构。 例如，假设 Azure Blob 存储中包含以下 JSON 文档：

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob 索引器将 JSON 文档分析成单个 Azure 搜索文档。 索引器通过将源中的“text”、“datePublished”和“tags”与同名且类型相同的目标索引字段进行匹配，来加载索引。

如前所述，字段映射不是必需的。 假设某个索引包含“text”、“datePublished”和“tags”字段，则请求不需要存在字段映射，Blob 索引器就能推断正确的映射。

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>分析 JSON 数组

或者，可以使用 JSON 数组选项。 如果 Blob 包含正确格式的 JSON 对象的数组，并且你希望每个元素都成为单独的 Azure 搜索文档时，则此选项非常有用。  例如，对于以下 JSON Blob，可以使用三个单独的文档（每个文档包含“id”和“text”字段）填充 Azure 搜索。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

对于 JSON 数组，索引器定义应如以下示例所示。 请注意，parsingMode 参数指定 `jsonArray` 分析器。 指定适当的分析器和提供适当的数据输入，是为 JSON Blob 编制索引所要满足的，专门与数组相关的唯一两项要求。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
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

## <a name="parse-nested-arrays"></a>分析嵌套数组
对于包含嵌套元素的 JSON 数组，可以指定 `documentRoot` 来指示多级别结构。 例如，如果 blob 如下所示：

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

## <a name="parse-blobs-separated-by-newlines"></a>分析换行符分隔的 Blob

如果 Blob 包含以换行符分隔的多个 JSON 实体，并且你希望每个元素成为独立的 Azure 搜索文档，则可以选择“JSON 行”选项。 例如，对于以下 Blob（其中包含三个不同的 JSON 实体），可以使用三个独立的文档（每个文档包含“id”和“text”字段）填充 Azure 搜索索引。

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

对于 JSON 行，索引器定义应如以下示例所示。 请注意，parsingMode 参数指定 `jsonLines` 分析器。 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

同样请注意，与 `jsonArray` 分析模式一样，可以省略字段映射。

## <a name="add-field-mappings"></a>添加字段映射

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

## <a name="see-also"></a>另请参阅

+ [Azure 搜索中的索引器](search-indexer-overview.md)
+ [使用 Azure 搜索为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)
+ [使用 Azure 搜索 Blob 索引器为 CSV Blob 编制索引](search-howto-index-csv-blobs.md)
+ [教程：在 Azure Blob 存储中搜索半结构化数据](search-semi-structured-data.md)
