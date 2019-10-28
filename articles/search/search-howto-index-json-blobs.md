---
title: 用于全文搜索的 Azure Blob 索引器中的 JSON blob 索引
titleSuffix: Azure Cognitive Search
description: 使用 Azure 认知搜索 Blob 索引器爬行文本内容的 Azure JSON blob。 索引器可自动为所选数据源（如 Azure Blob 存储）引入数据。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f8ddec95b92121c8dad4a39cf0c7b3f1798ec8ad
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789503"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>如何在 Azure 中使用 Blob 索引器为 JSON blob 编制索引认知搜索

本文介绍如何配置 Azure 认知搜索 blob[索引器](search-indexer-overview.md)，以便从 azure blob 存储中的 JSON 文档中提取结构化内容并使其在 azure 认知搜索中可搜索。 此工作流创建 Azure 认知搜索索引，并使用从 JSON blob 中提取的现有文本加载该索引。 

可以使用[门户](#json-indexer-portal)、[REST API](#json-indexer-rest) 或 [.NET SDK](#json-indexer-dotnet) 为 JSON 内容编制索引。 所有方法共有的是 JSON 文档位于 Azure 存储帐户中的 blob 容器中。 有关将 JSON 文档推送到其他非 Azure 平台的指南，请参阅[Azure 认知搜索中的数据导入](search-what-is-data-import.md)。

Azure Blob 存储中的 JSON blob 通常是单个 JSON 文档（分析模式为 `json`）或 JSON 实体的集合。 对于集合，该 blob 可以具有格式正确的 JSON 元素**数组**（分析模式为 `jsonArray`）。 Blob 还可以由由换行符分隔的多个单独的 JSON 实体组成（分析模式为 `jsonLines`）。 请求上的**parsingMode**参数确定输出结构。

> [!NOTE]
> 若要详细了解单个 blob 中的多个搜索文档的索引，请参阅[一对多索引](search-howto-index-one-to-many-blobs.md)。

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>使用门户

为 JSON 文档编制索引的最简单方法是使用 [Azure 门户](https://portal.azure.com/)中的向导。 通过分析 Azure Blob 容器中的元数据，[**导入数据**](search-import-data-portal.md)向导可以创建默认索引、将源字段映射到目标索引字段，并以单个操作加载索引。 根据源数据的大小和复杂性，在数分钟内就能创建一个有效的全文搜索索引。

建议为 Azure 认知搜索和 Azure 存储使用相同的 Azure 订阅，最好是在同一区域中。

### <a name="1---prepare-source-data"></a>1 - 准备源数据

[登录到 Azure 门户](https://portal.azure.com/)并[创建一个包含数据的 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 可以将公共访问级别设置为其任何有效值。

需要使用存储帐户名称、容器名称和访问密钥，才能在 "**导入数据**" 向导中检索数据。

### <a name="2---start-import-data-wizard"></a>2 - 启动“导入数据”向导

在搜索服务的 "概述" 页中，可以从命令栏[启动向导](search-import-data-portal.md)。

   ![门户中的 "导入数据" 命令](./media/search-import-data-portal/import-data-cmd2.png "启动“导入数据”向导")

### <a name="3---set-the-data-source"></a>3 - 设置数据源

在“数据源”页中，源必须是“Azure Blob 存储”，其规范如下：

+ “要提取的数据”应是“内容和元数据”。 选择此选项可让向导推断索引架构并映射要导入的字段。
   
+ **分析模式**应该设置为*json*、 *json 数组*或*json 行*。 

  “JSON”将每个 Blob 表达为单个搜索文档，在搜索结果中作为独立的项显示。 

  *Json 数组*适用于包含格式正确的 json 数据的 blob，格式正确的 json 对应于对象的数组，或具有一个属性，该属性是一个对象数组，您希望每个元素都被表述为独立的独立搜索文档。 如果 Blob 比较复杂，而且你未选择“JSON 数组”，则会将整个 Blob 作为单个文档引入。

  *JSON 行*用于由多个以新行分隔的 JSON 实体组成的 blob，你希望每个实体被表述为独立的独立搜索文档。 如果 blob 是复杂的，并且没有选择*JSON 行*分析模式，则整个 blob 引入为单个文档。
   
+ “存储容器”必须指定你的存储帐户和容器，或指定解析成容器的连接字符串。 可在 Blob 服务门户页上获取连接字符串。

   ![Blob 数据源定义](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - 跳过向导中的“添加认知搜索”页

导入 JSON 文档不需要添加认知技能。 除非有特定需要[将 AI 扩充添加](cognitive-search-concept-intro.md)到索引管道，否则应跳过此步骤。

若要跳过该步骤，请先转到下一页。

   ![认知搜索的下一页按钮](media/search-get-started-portal/next-button-add-cog-search.png)

在该页中，可以直接跳到索引自定义。

   ![跳过认知技能步骤](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - 设置索引属性

在“索引”页中，应会看到带有数据类型的字段列表，以及一系列用于设置索引属性的复选框。 此向导可以基于元数据生成字段列表，并通过采样源数据。 

通过单击属性列顶部的复选框，可以批量选择属性。 对于应返回到客户端应用并受全文搜索处理的每个字段，选择 "可**检索**和可**搜索**"。 您会注意到整数不是全文或模糊搜索（数字按原义计算，在筛选器中通常很有用）。

有关详细信息，请查看[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的说明。 

花费片刻时间来检查所做的选择。 运行向导后，将创建物理数据结构，到时，除非删除再重新创建所有对象，否则无法编辑这些字段。

   ![Blob 索引定义](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 创建索引器

完全指定设置后，向导将在搜索服务中创建三个不同的对象。 数据源对象和索引对象保存为 Azure 认知搜索服务中的已命名资源。 最后一个步骤创建索引器对象。 为索引器命名可让它作为独立的资源存在，无论在同一向导序列中创建了哪种索引和数据源对象，都可以计划和管理该索引器。

如果你不熟悉索引器，*索引器*是 Azure 认知搜索中的资源，用于对外部数据源进行爬网搜索。 **导入数据**向导的输出是一个索引器，该索引器对 JSON 数据源进行爬网，提取可搜索的内容，然后将其导入 Azure 认知搜索的索引。

   ![Blob 索引器定义](media/search-howto-index-json/import-wizard-json-indexer.png)

单击“确定”运行向导并创建所有对象。 随后会立即开始编制索引。

可以在门户页监视数据导入。 进度通知指示索引状态以及已上传的文档数。 

索引编制完成后，可以使用[搜索浏览器](search-explorer.md)来查询索引。

> [!NOTE]
> 如果看不到预期的数据，则可能需要对更多字段设置更多属性。 删除你刚创建的索引和索引器，并再次逐句通过向导，同时修改步骤5中索引属性所做的选择。 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

对于 Azure 中的所有索引器通用的三部分工作流，可以使用 REST API 来索引 JSON blob 认知搜索：创建数据源、创建索引、创建索引器。 提交 Create 索引器请求时，将发生从 blob 存储中提取的数据。 完成此请求后，将具有可查询的索引。 

可以在本部分末尾查看[REST 示例代码](#rest-example)，其中显示了如何创建所有三个对象。 本部分还包含有关[JSON 分析模式](#parsing-modes)、[单个 blob](#parsing-single-blobs)、 [JSON 数组](#parsing-arrays)和[嵌套数组](#nested-json-arrays)的详细信息。

对于基于代码的 JSON 索引，请使用[Postman](search-get-started-postman.md)和 REST API 来创建这些对象：

+ [编入](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [编制](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

操作顺序要求按此顺序创建和调用对象。 与门户工作流相反，代码方法需要一个可用的索引来接受通过**创建索引器**请求发送的 JSON 文档。

Azure Blob 存储中的 JSON blob 通常是单个 JSON 文档或 JSON "数组"。 Azure 认知搜索中的 blob 索引器可以分析任意构造，具体取决于你对请求设置**parsingMode**参数的方式。

| JSON 文档 | parsingMode | 描述 | 可用性 |
|--------------|-------------|--------------|--------------|
| 每个 Blob 各有一个 | `json` | 将 JSON Blob 分析为单个文本区块。 每个 JSON blob 都将成为单个 Azure 认知搜索文档。 | 在[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和[.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 中公开提供。 |
| 每个 Blob 有多个 | `jsonArray` | 分析 blob 中的 JSON 数组，其中数组的每个元素都成为单独的 Azure 认知搜索文档。  | 在[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和[.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 中公开提供。 |
| 每个 Blob 有多个 | `jsonLines` | 分析包含由换行符分隔的多个 JSON 实体（"数组"）的 blob，其中每个实体将成为单独的 Azure 认知搜索文档。 | 在[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和[.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 中公开提供。 |

### <a name="1---assemble-inputs-for-the-request"></a>1-汇集请求的输入

对于每个请求，必须提供 Azure 认知搜索的服务名称和管理密钥（在 POST 标头中）以及 blob 存储的存储帐户名称和密钥。 可以使用[Postman](search-get-started-postman.md)将 HTTP 请求发送到 Azure 认知搜索。

将以下四个值复制到记事本中，以便可以将它们粘贴到请求中：

+ Azure 认知搜索服务名称
+ Azure 认知搜索管理密钥
+ Azure 存储帐户名称
+ Azure 存储帐户密钥

可以在门户中找到以下值：

1. 在 Azure 认知搜索的门户页中，从 "概述" 页复制 "搜索服务 URL"。

2. 在左侧导航窗格中，单击 "**密钥**"，然后复制 "主密钥" 或 "辅助密钥" （它们等效）。

3. 切换到存储帐户的门户页。 在左侧导航窗格中的 "**设置**" 下，单击 "**访问密钥**"。 此页提供帐户名和密钥。 将存储帐户名称和密钥之一复制到记事本。

### <a name="2---create-a-data-source"></a>2-创建数据源

此步骤提供索引器使用的数据源连接信息。 数据源是 Azure 认知搜索中的命名对象，用于保存连接信息。 数据源类型 `azureblob` 确定索引器调用哪些数据提取行为。 

将有效值替换为 "服务名称"、"管理密钥"、"存储帐户" 和 "帐户密钥占位符"。

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3-创建目标搜索索引 

索引器与索引架构配对。 如果使用的是 API（而不是门户），请提前准备索引，以便可以在索引器操作中指定它。

此索引存储 Azure 认知搜索中的可搜索内容。 若要创建索引，请提供一个架构，用于在文档、属性和其他构造中指定可以塑造搜索体验的字段。 如果创建与源具有相同字段名称和数据类型的索引，索引器将会匹配源和目标字段，使你无需显式映射字段。

以下示例演示了一个[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)请求。 该索引包含一个可搜索的 `content` 字段，该字段存储从 Blob 提取的文本：   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4-配置并运行索引器

与索引和数据源一样，索引器也是在 Azure 认知搜索服务上创建和重用的命名对象。 创建索引器的完全指定的请求可能如下所示：

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

索引器配置位于请求正文中。 它需要数据源和 Azure 认知搜索中已存在的空目标索引。 

计划和参数是可选的。 如果省略它们，索引器将立即运行，并使用 `json` 作为分析模式。

此特定索引器不包含字段映射。 在索引器定义中，如果源 JSON 文档的属性与目标搜索索引的字段相匹配，则可以保留**字段映射**。 


### <a name="rest-example"></a>REST 示例

本部分概述了用于创建对象的所有请求。 有关组件部件的讨论，请参阅本文前面的部分。

### <a name="data-source-request"></a>数据源请求

所有索引器都需要一个为现有数据提供连接信息的数据源对象。 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>索引请求

所有索引器都需要一个接收数据的目标索引。 请求的主体定义索引架构（包含字段），这些字段不支持可搜索索引中所需的行为。 运行索引器时，此索引应为空。 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>索引器请求

此请求显示一个完全指定的索引器。 它包括前面的示例中省略的字段映射。 请记住，只要有可用的默认值，"schedule"、"parameters" 和 "fieldMappings" 都是可选的。 省略 "schedule" 将导致立即运行索引器。 省略 "parsingMode" 将导致索引使用 "json" 默认值。

在 Azure 上创建索引器认知搜索触发数据导入。 它会立即运行，如果你提供了一个计划，则按计划运行。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

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

.NET SDK 与 REST API 具有完全的奇偶校验。 我们建议查看前面的 REST API 部分，以了解相关概念、工作流和要求。 然后，可以参阅以下 .NET API 参考文档，在托管代码中实现 JSON 索引器。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>分析模式

JSON blob 可以采用多种形式。 JSON 索引器上的**parsingMode**参数确定如何在 Azure 认知搜索索引中分析和构造 json blob 内容：

| parsingMode | 描述 |
|-------------|-------------|
| `json`  | 将每个 blob 作为单个文档进行索引。 这是默认值。 |
| `jsonArray` | 如果 blob 包含 JSON 数组，并且你需要数组的每个元素成为 Azure 认知搜索中的单独文档，请选择此模式。 |
|`jsonLines` | 如果你的 blob 由多个 JSON 实体组成，并由新行分隔，你需要每个实体成为 Azure 认知搜索中的单独文档，请选择此模式。 |

可将某个文档视为搜索结果中的单个项。 如果希望数组中的每个元素都作为独立项显示在搜索结果中，请根据需要使用 "`jsonArray`" 或 "`jsonLines`" 选项。

在索引器定义中，可以选择性地使用[字段映射](search-indexer-field-mappings.md)来选择要将源 JSON 文档的哪些属性用于填充目标搜索索引。 对于 `jsonArray` 分析模式，如果数组作为较低级别的属性存在，则可以设置文档根目录，指示数组在 blob 中的放置位置。

> [!IMPORTANT]
> 当你使用 `json`、`jsonArray` 或 `jsonLines` 分析模式时，Azure 认知搜索假定数据源中的所有 blob 都包含 JSON。 如果需要在同一数据源中支持混合使用 JSON 和非 JSON blob，请通过[我们的 UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search)告知我们。


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>分析单个 JSON blob

默认情况下， [Azure 认知搜索 blob 索引器](search-howto-indexing-azure-blob-storage.md)将 JSON blob 分析为单个文本块。 通常会希望保留 JSON 文档的结构。 例如，假设 Azure Blob 存储中包含以下 JSON 文档：

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob 索引器将 JSON 文档解析为单个 Azure 认知搜索文档。 索引器通过将源中的“text”、“datePublished”和“tags”与同名且类型相同的目标索引字段进行匹配，来加载索引。

如前所述，字段映射不是必需的。 假设某个索引包含“text”、“datePublished”和“tags”字段，则请求不需要存在字段映射，Blob 索引器就能推断正确的映射。

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>分析 JSON 数组

或者，可以使用 JSON array 选项。 当 blob 包含*格式正确的 JSON 对象的数组*，并且你希望每个元素成为单独的 Azure 认知搜索文档时，此选项很有用。 例如，假设有以下 JSON blob，则可以用三个单独的文档（每个文档都带有 "id" 和 "文本" 字段）填充 Azure 认知搜索索引。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

对于 JSON 数组，索引器定义应如以下示例所示。 请注意，parsingMode 参数指定 `jsonArray` 分析器。 为 JSON blob 编制索引时，指定正确的分析器并具有正确的数据输入是唯一两个特定于数组的要求。

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
对于包含嵌套元素的 JSON 数组，你可以指定一个 `documentRoot` 来指示多级别结构。 例如，如果 blob 如下所示：

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

## <a name="parse-blobs-separated-by-newlines"></a>分析由换行符分隔的 blob

如果 blob 包含由换行符分隔的多个 JSON 实体，并且你希望每个元素成为单独的 Azure 认知搜索文档，则可以选择 "JSON 行" 选项。 例如，给定以下 blob （其中有三个不同的 JSON 实体），可以用三个单独的文档填充 Azure 认知搜索索引，每个文档都有 "id" 和 "文本" 字段。

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

对于 JSON 行，索引器定义应类似于下面的示例。 请注意，parsingMode 参数指定 `jsonLines` 分析器。 

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

同样，请注意，可以省略字段映射，这类似于 `jsonArray` 分析模式。

## <a name="add-field-mappings"></a>添加字段映射

当源与目标字段未完美对齐时，可以在请求正文中定义一个字段映射节，以建立显式的字段间关联。

目前，Azure 认知搜索无法直接为任意 JSON 文档编制索引，因为它仅支持基元数据类型、字符串数组和 GeoJSON 点。 不过，可以使用**字段映射**选取 JSON 文档的部分，然后将它们“提升”到搜索文档的顶级字段。 若要了解字段映射的基础知识，请参阅[Azure 中的字段映射认知搜索索引器](search-indexer-field-mappings.md)。

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

+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [用 Azure 认知搜索为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)
+ [用 Azure 认知搜索 blob 索引器为 CSV blob 编制索引](search-howto-index-csv-blobs.md)
+ [教程：从 Azure Blob 存储搜索半结构化数据](search-semi-structured-data.md)
