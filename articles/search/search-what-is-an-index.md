---
title: 创建索引定义和概念 - Azure 搜索
description: 介绍 Azure 搜索中的索引术语和概念，包括组成部分和物理结构。
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000943"
---
# <a name="create-a-basic-index-in-azure-search"></a>在 Azure 搜索中创建基本索引

在 Azure 搜索中，*索引*是用于在 Azure 搜索服务上进行筛选和全文搜索的*文档*和其他构造的持久存储。 从概念上讲，文档是索引中的一个可搜索数据单元。 例如，电子商务零售商可能有所销售每件商品的文档，新闻机构可能有每篇报道的文档。 将这些概念对应到更为熟悉的数据库等效对象：*索引*在概念上类似于*表*，*文档*大致相当于表中的*行*。

添加或上传索引时，Azure 搜索将根据提供的架构创建物理基础结构。 例如，如果将索引中的某个字段标记为可搜索，则为该字段创建倒排索引。 以后在 Azure 搜索中添加或上传文档或者提交搜索查询时，将向搜索服务中的特定索引发送请求。 加载包含文档值的字段称为索引编制或数据引入。

可以在门户、[REST API](search-create-index-rest-api.md) 或 [.NET SDK](search-create-index-dotnet.md) 中创建索引。

## <a name="components-of-an-index"></a>索引的组成部分

从架构上讲，Azure 搜索索引由以下元素组成。 

[*字段集合*](#fields-collection)通常是索引的最大组成部分，其中每个字段都已命名、类型化，并具有允许行为的属性（确定该字段的用法）。 其他元素包括[建议器](#suggesters)、[评分配置文件](#scoring-profiles)、[分析器](#analyzers)（其组成部分用于支持自定义项）和 [CORS](#cors) 选项。

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>字段集合与属性
定义架构时，必须在索引中指定每个字段的名称、类型和属性。 字段类型的作用是对该字段中存储的数据进行分类。 对各个字段设置属性的目的是指定字段的使用方式。 下表枚举了可以指定的类型和属性。

### <a name="data-types"></a>数据类型
| Type | 说明 |
| --- | --- |
| *Edm.String* |全文搜索可以选择性地标记化（断词、词干提取等）的文本。 |
| *Collection(Edm.String)* |全文搜索可以选择性标记化的字符串列表。 理论上，集合中的项目数没有上限，但集合的有效负载大小上限为 16 MB。 |
| *Edm.Boolean* |包含 true/false 值。 |
| *Edm.Int32* |32 位整数值。 |
| *Edm.Int64* |64 位整数值。 |
| *Edm.Double* |双精度数字数据。 |
| *Edm.DateTimeOffset* |以 OData V4 格式表示的日期时间值（例如 `yyyy-MM-ddTHH:mm:ss.fffZ` 或 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`。 |
| *Edm.GeographyPoint* |表示地球上的地理位置的点。 |

若要深入了解 Azure 搜索支持的数据类型，请参阅[此处](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)。

### <a name="index-attributes"></a>索引属性
| 属性 | 说明 |
| --- | --- |
| *Key* |为每个文档提供唯一 ID 用于查找文档的字符串。 每个索引必须有一个 key。 只有一个字段可以是 key，并且此字段类型必须设置为 Edm.String。 |
| *Retrievable* |指定是否可以在搜索结果中返回字段。 |
| *Filterable* |允许在筛选查询中使用字段。 |
| *Sortable* |允许查询使用此字段对搜索结果排序。 |
| *Facetable* |允许在 [分面导航](search-faceted-navigation.md) 结构中使用字段进行用户自主筛选。 通常，包含重复值的字段更适合分面导航，这些重复值可用于将多个文档（例如，同属一个品牌或服务类别的多个文档）组合在一起。 |
| *Searchable* |将字段标记为可全文搜索。 |

若要深入了解 Azure 搜索的索引属性，请参阅[此处](https://docs.microsoft.com/rest/api/searchservice/Create-Index)。

## <a name="suggesters"></a>建议器
建议器是定义要使用索引中的哪些字段来支持搜索中的自动填写或提前键入查询的架构部分。 当用户尝试键入搜索查询时，通常将部分搜索字符串发送到“建议”（Azure 搜索服务 REST API），并该 API 返回一组建议的短语。 在索引中定义的建议器确定哪些字段用于生成提前键入的搜索词。 有关配置详细信息，请参阅[添加建议器](index-add-suggesters.md)。

## <a name="scoring-profiles"></a>为配置文件评分

评分配置文件是定义自定义评分行为，方便用户影响搜索结果中排名更高的项的架构部分。 计分配置文件由字段权重和函数组成。 若要使用它们，请在查询字符串上按名称指定配置文件。

在幕后运行的默认计分概要文件，用于为结果集中的每个项目计算搜索分数。 可使用内部、未命名的计分概要文件。 或者，将 defaultScoringProfile 设置为使用自定义配置文件作为默认配置文件，每当未在查询字符串上指定自定义配置文件时，将调用该配置文件。

有关详细信息，请参阅[添加评分配置文件](index-add-scoring-profiles.md)。

## <a name="analyzers"></a>分析器

分析器元素设置用于字段的语言分析器的名称。 有关允许的值集，请参阅 [Azure 搜索中的语言分析器](index-add-language-analyzers.md)。 此选项只能用于可搜索字段，并且无法与 **searchAnalyzer** 或 **indexAnalyzer** 一起设置。 选择分析器后，无法为字段更改它。

## <a name="cors"></a>CORS

默认情况下，客户端 JavaScript 无法调用任何 API，因为浏览器将阻止所有跨域请求。 若要允许对索引进行跨域查询，请通过设置 **corsOptions** 来启用 CORS（跨域资源共享）。 出于安全原因，只有查询 API 才支持 CORS。 

可为 CORS 设置以下选项：

+ **allowedOrigins**（必需）：这是会被授予索引访问权限的来源的列表。 这意味着，将允许从这些来源提供的任何 JavaScript 代码查询索引（假设它提供正确的 api-key）。 每个来源通常采用 `protocol://<fully-qualified-domain-name>:<port>` 格式，不过往往会省略 `<port>`。 有关更多详细信息，请参阅[跨域资源共享 (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)。

  若要允许访问所有来源，请将 `*` 作为单个项目包含在 **allowedOrigins** 数组中。 不建议对生产搜索服务采用这种做法，但它在开发和调试中却很有用。

+ **maxAgeInSeconds**（可选）：浏览器使用此值确定缓存 CORS 预检响应的持续时间（以秒为单位）。 此值必须是非负整数。 此值越大，性能越好，但 CORS 策略更改生效所需的时间也越长。 如果未设置此值，将使用 5 分钟的默认持续时间。

## <a name="next-steps"></a>后续步骤

了解索引的构成后，可以继续在门户中创建第一个索引。

> [!div class="nextstepaction"]
> [添加索引（门户）](search-create-index-portal.md)