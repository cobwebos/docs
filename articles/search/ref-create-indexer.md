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
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786916"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>创建索引器（Azure 搜索服务 REST api-version=2017-11-11-Preview）

此 API 引用是文档的预览版本，涵盖了对索引的认知搜索增强功能。

与[正式发布版](https://docs.microsoft.com/rest/api/searchservice/create-indexer)一样，可使用 HTTP POST 请求在 Azure 搜索服务中创建新的索引器。 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
api-key 必须是管理员密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅 [Azure 搜索中的安全性](search-security-overview.md)中的身份验证部分。 [在门户中创建 Azure 搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

此外，可使用 PUT 并在 URI 上指定数据源名称。 如果数据源不存在，则创建它。  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
需要 api-version。 当前版本为 `2016-09-01`。 有关详细信息，请参阅 [Azure 搜索中的 API 版本](search-api-versions.md)。

有关创建索引器的特定于数据平台的指南，请从[索引器概述](search-indexer-overview.md)入门，其中包含[相关文章](search-indexer-overview.md#next-steps)的完整列表。

> [!NOTE]  
>  允许的最大索引器数因定价层而异。 免费服务允许最多 3 个索引器。 标准服务允许 50 个索引器。 有关详细信息，请参阅[服务限制](search-limits-quotas-capacity.md)。    

## <a name="request"></a>请求  
 请求正文包含索引器定义，可指定用于编制索引的数据源和目标索引以及可选的索引计划和参数。  

 构造请求负载的语法如下。 本主题稍后会提供一个请求示例。  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>索引器计划  
一个索引器可以选择指定一个计划。 如果存在计划，则索引器按计划定期运行。 计划程序是内置的，无法使用外部计划程序。 计划具有以下属性： 

-   **间隔**：必需。 指定索引器运行的间隔或时段的持续时间值。 允许的最小间隔为 5 分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 此模式为：`"P[nD][T[nH][nM]]".` 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。  

-   **startTime**：可选。 索引器应开始运行时的 UTC 日期/时间。  

### <a name="indexer-parameters"></a>索引器参数  
 索引器可以选择指定影响其行为的多个参数。 下面列出的所有参数都是可选的。  

-   **maxFailedItems**：索引器运行被视为失败前，允许索引失败的项目数。 默认值为 0。 [获取索引器状态（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)操作将返回有关失败的项目的信息。  

-   **maxFailedItemsPerBatch**：索引器运行被视为失败前，每批中允许索引失败的项目数。 默认值为 0。  

-   **batchSize**：指定从数据源读取并将索引编制为单个批处理以提高性能的项数。 默认值取决于数据源类型：对于 Azure SQL 和 Azure Cosmos DB，默认值为 1000；对于 Azure Blob 存储，默认值为 10。

### <a name="field-mapping-parameters"></a>字段映射参数

索引器定义包含字段关联，用于将源字段映射到 Azure 搜索索引中的目标字段。 根据内容传输是遵循直接的路径还是扩充的路径，有两种类型的关联：

+ **fieldMappings**为可选关联，可在源目标字段名称不匹配或要指定函数时使用。
+ **outputFieldMappings** 是生成[扩充管道](cognitive-search-concept-intro.md)时必需的关联。 在扩充管道中，输出字段是在扩充过程中定义的结构。 例如，输出字段可以是源文档中两个单独字段的扩充期间生成的复合结构。 

#### <a name="fieldmappings"></a>fieldMappings

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

#### <a name="outputfieldmappings"></a>outputFieldMappings

在需要将技能组合绑定到索引器的认知搜索场景中，必须添加 `outputFieldMappings` 以关联扩充步骤的任何输出，扩充步骤用于向索引中可搜索字段提供内容。

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

#### <a name="field-mapping-functions"></a>字段映射函数

字段映射还可用于使用字段映射函数转换源字段值。 例如，一个任意字符串值可以是 base64 编码的，所以它可以用来填充文档密钥字段。

若要详细了解何时以及如何使用字段映射函数，请参阅[字段映射函数](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)。

### <a name="request-body-examples"></a>请求正文示例  
 下面的示例创建一个索引器，该索引器按计划（从 2015 年 1 月 1 日 UTC 开始，每小时运行一次）将数据从 `ordersds` 数据源引用的表复制到 `orders` 索引。 如果每批中索引失败的项目不超过 5 个并且索引失败的项目总计不超过 10 个，则每个索引器都将成功调用。  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>响应  
 对于成功的请求，返回“201 已创建”。  

## <a name="see-also"></a>另请参阅

+ [认知搜索概述](cognitive-search-concept-intro.md)
+ [快速入门：尝试认知搜索](cognitive-search-quickstart-blob.md)
+ [如何映射字段](cognitive-search-output-field-mapping.md)
