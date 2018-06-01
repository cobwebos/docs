---
title: 创建索引器（Azure 搜索服务 REST api-version=2017-11-11-Preview）
description: 在预览版 API 中，索引器将扩展为包含 outputFieldMapping 参数，用于将扩充输出映射到 Azure 搜索索引中的某个字段。
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365179"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>创建索引器（Azure 搜索服务 REST api-version=2017-11-11-Preview）

此 API 参考是文档的特定于预览版的版本，向创建索引器 API 添加了[认知搜索](cognitive-search-concept-intro.md)元素。 与[正式发布版](https://docs.microsoft.com/rest/api/searchservice/create-indexer)一样，可使用 HTTP POST 请求在 Azure 搜索服务中创建新的索引器。 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
api-key 必须是管理员密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅 [Azure 搜索中的安全性](search-security-overview.md)中的身份验证部分。 [在门户中创建 Azure 搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

此外，可使用 PUT 并在 URI 上指定数据源名称。 如果数据源不存在，则创建它。  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
需要 api-version。 目前的正式发布版本为 `api-version=2017-11-11`，但你需要认知搜索的预览版：`api-version=2017-11-11-Preview`。  有关详细信息，请参阅 [Azure 搜索中的 API 版本](search-api-versions.md)。

有关创建索引器的特定于数据平台的指南，请从[索引器概述](search-indexer-overview.md)入门，其中包含[相关文章](search-indexer-overview.md#next-steps)的完整列表。

> [!NOTE]  
>  允许的最大索引器数因定价层而异。 免费服务允许最多 3 个索引器。 标准服务允许 50 个索引器。 标准高清服务根本不支持索引器。 有关详细信息，请参阅[服务限制](search-limits-quotas-capacity.md)。    

## <a name="request"></a>请求  

[数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)、[索引](https://docs.microsoft.com/rest/api/searchservice/create-index)和[技术集](ref-create-skillset.md)是[索引器](search-indexer-overview.md)定义的一部分，但每个都是独立的组件，可以按不同组合进行使用。 例如，可以将同一数据源与多个索引器配合使用、将同一索引与多个索引器配合使用，或者让多个索引器写入单个索引。

 请求正文包含索引器定义，其中包括以下部分。

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parameters](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>请求语法

构造请求有效负载的语法如下。 本文稍后会提供一个请求示例。  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

[数据源定义](https://docs.microsoft.com/rest/api/searchservice/create-data-source)通常包含可供索引器用来利用源平台特征的属性。 因此，传递给索引器的数据源决定了某些属性和参数（例如 Azure Blob 中的内容类型筛选或 Azure SQL 数据库的查询超时）的可用性。 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

[索引架构](https://docs.microsoft.com/rest/api/searchservice/create-index)定义的字段集合包含可搜索的、可筛选的、可检索的和其他类型的属性，这些属性决定了字段的使用方式。 在编制索引期间，索引器会对数据源进行爬网，并可选择破解文档和提取信息，将结果序列化为 JSON 格式，然后根据为索引定义的架构为有效负载编制索引。

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[认知搜索（预览版）](cognitive-search-concept-intro.md)是指 Azure 搜索中的自然语言和图像处理功能，在数据引入期间应用，目的是提取条目、关键短语、语言、图像信息，等等。 应用于内容的转换是通过技术进行的，这些技术可以组合成单个[技术集](ref-create-skillset.md)，一个索引器一个技术集。 与数据源和索引一样，技术集是独立的组件，可以附加到索引器。 可以通过其他索引器重新调整技术集的用途，但每个索引器一次只能使用一个技术集。
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"schedule"  
一个索引器可以选择指定一个计划。 在没有计划的情况下，索引器会在你发送请求后立即运行：连接到数据源，然后对其进行爬网和编制索引操作。 某些情况（例如长时间运行编制索引作业）下，会使用计划来[延长处理时间](search-howto-reindex.md#scale-out-indexing)（超出 24 小时的最长处理时间限制）。 如果存在计划，则索引器按计划定期运行。 计划程序是内置的，无法使用外部计划程序。 **计划**具有以下属性： 

-   **间隔**：必需。 指定索引器运行的间隔或时段的持续时间值。 允许的最小间隔为五分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 此模式为：`"P[nD][T[nH][nM]]".` 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。  

-   **startTime**：可选。 索引器应开始运行时的 UTC 日期/时间。  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"parameters"

索引器可以选择使用配置参数来修改运行时行为。 配置参数可以根据索引器请求进行逗号分隔。 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>适用于所有索引器的通用参数

| 参数 | 类型和允许的值   | 使用情况  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Integer<br/>默认值特定于源（Azure SQL 数据库和 Azure Cosmos DB 为 1000，Azure Blob 存储为 10） | 指定可从数据源读取并可将其索引编制为单个批次以提高性能的项数。 |
| `"maxFailedItems"` | Integer<br/>默认值为 0 | 在将索引器运行视为失败之前可以允许的错误数。 如果不希望因任何错误而导致索引编制过程停止，请将此项设置为 -1。 可以使用[获取索引器状态](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)操作检索有关失败项目的信息。  |
| `"maxFailedItemsPerBatch"` | Integer<br/>默认值为 0 | 在将索引器运行视为失败之前可以允许的每个批次中的错误数。 如果不希望因任何错误而导致索引编制过程停止，请将此项设置为 -1。 |

#### <a name="blob-configuration-parameters"></a>Blob 配置参数

多个参数专用于特定的索引器，例如 [Azure Blob 索引编制](search-howto-indexing-azure-blob-storage.md)。

| 参数 | 类型和允许的值   | 使用情况  |
|-----------|---------------------------|--------|
| `"parsingMode"` | String<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)，设置为 `text` 可改进 Blob 存储中纯文本文件的索引编制性能。 <br/>对于 [CSV Blob](search-howto-index-csv-blobs.md)，请在 Blob 为纯 CSV 文件时设置为 `delimitedText`。 <br/>对于 [JSON Blob](search-howto-index-json-blobs.md)，可以设置为 `json` 以提取结构化内容，也可以设置为 `jsonArray`（预览版）以将数组的单个元素提取为 Azure 搜索中的单独文档。 |
| `"excludedFileNameExtensions"` | String<br/>逗号分隔的列表 | 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)，请忽略列表中的任何文件类型。 例如，可以排除“.png、.png、.mp4”，在索引编制过程中遇到这些文件时直接跳过。 | 
| `"indexedFileNameExtensions"` | String<br/>逗号分隔的列表 | 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)，如果文件扩展名在列表中，请选择 Blob。 例如，可以让索引编制侧重于特定的应用程序文件“.docx、.pptx、.msg”，以便专门包括这些文件类型。 | 
| `"failOnUnsupportedContentType"` | 是 <br/>false（默认值） | 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)，如果希望在遇到不受支持的内容类型时继续进行索引编制，并且无法提前知道所有内容类型（文件扩展名），则请设置为 `false`。 |
| `"failOnUnprocessableDocument"` | 是 <br/>false（默认值）| 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)，如果希望在文档的索引编制失败的情况下继续进行索引编制，则请设置为 `false`。 |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | 是 <br/>false（默认值）| 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)，请将此属性设置为 `true`，这样就仍可为因太大而无法处理的 Blob 内容编制存储元数据的索引。  过大的 blob 会被默认视为错误。 有关 Blob 大小的限制，请参阅[服务限制](search-limits-quotas-capacity.md)。 |
| `"delimitedTextHeaders"` | String<br/>逗号分隔的列表| 对于 [CSV Blob（预览版）](search-howto-index-csv-blobs.md)，请指定一个包含列标题的逗号分隔的列表，以便将索引中的源字段映射到目标字段。 |
| `"delimitedTextDelimiter"` | String<br/>用户定义 | 对于 [CSV Blob（预览版）](search-howto-index-csv-blobs.md)，请为其中的每个行都会以新文档开头的 CSV 文件指定行尾分隔符（例如，`"|"`）。  |
| `"firstLineContainsHeaders"` | true（默认值） <br/>false | 对于 [CSV Blob（预览版）](search-howto-index-csv-blobs.md)，请指示每个 Blob 的第一行（非空）包含标头。|
| `"documentRoot"`  | String<br/>用户定义 | 对于 [JSON 数组（预览版）](search-howto-index-json-blobs.md#nested-json-arrays)，在给定一个结构化或半结构化文档的情况下，可以使用此属性指定数组的路径。 |
| `"dataToExtract"` | String<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"`（默认值） | 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)：<br/>设置为 `"storageMetadata"`，仅对[标准 Blob 属性和用户指定的元数据](../storage/blobs/storage-properties-metadata.md)编制索引。 <br/>设置为 `"allMetadata"` 可提取 Azure Blob 存储子系统提供的元数据，对[特定于内容类型的元数据](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata)（例如，只特定于 .png 文件的元数据）编制索引。 <br/>设置为 `"contentAndMetadata"` 可提取每个 Blob 的所有元数据和文本内容。 <br/><br/>对于[认知搜索（预览版）中的图像分析](cognitive-search-concept-image-scenarios.md)，将 `"imageAction"` 设置为 `"generateNormalizedImages"` 时，`"dataToExtract"` 设置会指示索引器从图像内容中提取哪些数据。 适用于 .PDF 或其他应用程序中的嵌入图像内容，或者 Azure Blob 中的图像文件（例如 .jpg 和 .png）。  |
| `"imageAction"` |  String<br/>`"none"`<br/>`"generateNormalizedImages"` | 对于 [Azure Blob](search-howto-indexing-azure-blob-storage.md)，设置为 `"none"` 即可忽略数据集中的嵌入图像或图像文件。 这是默认值。 <br/><br/>对于[认知搜索中的图像分析](cognitive-search-concept-image-scenarios.md)，设置为 `"generateNormalizedImages"` 即可从图像中提取文本（例如，禁行交通标志中的字“停”），并将其嵌入到内容字段中。 在图像分析过程中，索引器会在文档破解过程中生成一系列规范化的图像，并将生成的信息嵌入内容字段中。 此操作需要将 `"dataToExtract"` 设置为 `"contentAndMetadata"`。 规范化的图像是指在视觉搜索结果中包含图像时，对图像进行额外的处理，使图像的输出一致，并通过调整大小和旋转方向使图像在呈现时更一致（例如，使图像控件中的照片大小一致，如 [JFK 演示](https://github.com/Microsoft/AzureSearch_JFK_Files)中所示）。 此信息是为所使用的每个图像生成的。 |


#### <a name="other-configuration-parameters"></a>其他配置参数

以下参数特定于 Azure SQL 数据库。

| 参数 | 类型和允许的值   | 使用情况  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | String<br/>"hh:mm:ss"<br/>"00:05:00"| 对于 [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)，可以通过设置此参数使超时超出 5 分钟的默认值。|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

索引器定义包含字段关联，用于将源字段映射到 Azure 搜索索引中的目标字段。 根据内容传输是遵循直接的路径还是扩充的路径，有两种类型的关联：

+ **fieldMappings**为可选关联，可在源目标字段名称不匹配或要指定函数时使用。
+ **outputFieldMappings** 是生成[扩充管道](cognitive-search-concept-intro.md)时必需的关联。 在扩充管道中，输出字段是在扩充过程中定义的结构。 例如，输出字段可以是源文档中两个单独字段的扩充期间生成的复合结构。 

下例为具有字段 `_id` 的源表。 Azure 搜索不允许字段名称以下划线开头，必须重命名该字段。 可以使用索引器的 `fieldMappings` 属性完成此操作，如下所示：

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

可以指定多个字段映射：

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

源和目标字段名称不区分大小写。

若要了解字段映射的适用场景，请参阅[搜索索引器字段映射](https://docs.microsoft.com/azure/search/search-indexer-field-mappings)。

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

在将技术集绑定到索引器的[认知搜索](cognitive-search-concept-intro.md)方案中，必须添加 `outputFieldMappings` 来关联扩充步骤的任何输出，以便向索引中的可搜索字段提供内容。

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

### <a name="field-mapping-functions"></a>字段映射函数

字段映射还可用于使用字段映射函数转换源字段值。 例如，一个任意字符串值可以是 base64 编码的，所以它可以用来填充文档密钥字段。

若要详细了解何时以及如何使用字段映射函数，请参阅[字段映射函数](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)。

## <a name="request-examples"></a>请求示例  
 第一个示例创建一个索引器，该索引器按计划（从 2015 年 1 月 1 日（UTC 时间）开始，每小时运行一次）将数据从 `ordersds` 数据源引用的表复制到 `orders` 索引。 如果每批中索引失败的项目不超过 5 个并且索引失败的项目总计不超过 10 个，则每个索引器都将成功调用。  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

第二个示例演示认知搜索操作，由技术集的引用和 [outputFieldMappings](#output-fieldmappings) 来指示。 [技术集](ref-create-skillset.md)是高级资源，是单独定义的。 此示例是[认知搜索教程](cognitive-search-tutorial-blob.md)中的索引器定义的缩略版。

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>响应  
 对于成功的请求，返回“201 已创建”。  

## <a name="see-also"></a>另请参阅

+ [索引器概述](search-indexer-overview.md)
+ [认知搜索概述](cognitive-search-concept-intro.md)
+ [快速入门：尝试认知搜索](cognitive-search-quickstart-blob.md)
+ [如何映射字段](cognitive-search-output-field-mapping.md)
