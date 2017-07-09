---
title: "Azure 搜索服务 REST API 版本 Version 2015-02-28-Preview | Microsoft Docs"
description: "Azure 搜索服务 REST API 版本 2015-02-28-Preview 包括实验功能，如自然语言分析器和 moreLikeThis 搜索。"
services: search
documentationcenter: na
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2017
ms.author: brjohnst
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: e6ad5c964bfa8421be2706cb4015980e01a271b7
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017


---
# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>Azure 搜索服务 REST API：版本 2015-02-28-预览版
本文是 `api-version=2015-02-28-Preview` 的参考文档。 此预览版通过提供以下实验功能扩展了当前公开发布的版本 [api-version=2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx)：

* [搜索文档](#SearchDocs) API 中的 `moreLikeThis` 查询参数。 它查找与另一个特定文档相关的其他文档。

`2015-02-28-Preview` REST API 的一些其他部分单独进行介绍。 其中包括：

* [计分配置文件](search-api-scoring-profiles-2015-02-28-preview.md)
* [索引器](search-api-indexers-2015-02-28-preview.md)

Azure 搜索服务在多个版本内可用。 有关详细信息，请参考[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

## <a name="apis-in-this-document"></a>本文档中的 API
Azure 搜索服务 API 支持 API 操作的两个 URL 语法：简单和 OData（有关详细信息，请参阅[对 OData 的支持（Azure 搜索 API）](http://msdn.microsoft.com/library/azure/dn798932.aspx)）。 以下列表显示简单语法。

[创建索引](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[更新索引](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[获取索引](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[列出索引](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[获取索引统计信息](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[测试分析器](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[删除索引](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[添加、删除和更新索引内的数据](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[搜索文档](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[查找文档](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[计数文档](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[建议](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

- - -
<a name="IndexOps"></a>

## <a name="index-operations"></a>索引操作
可通过简单 HTTP 请求（POST、GET、PUT、DELETE）针对给定索引资源在 Azure 搜索服务中创建和管理索引。 若要创建索引，先 POST 一个描述索引架构的 JSON 文档。 该架构定义索引的字段、其数据类型以及可如何使用它们（例如，在全文搜索、筛选器、排序或分面中）。 它还定义计分配置文件、建议器和其他属性来配置语法的行为。

以下示例演示用于使用以两种语言定义的“Description”字段搜索酒店信息的架构。 请注意属性如何控制字段的使用方式。 例如，`hotelId` 用作文档键 (`"key": true`) 并从全文搜索 (`"searchable": false`) 中排除。

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

创建索引后，将上传填充索引的文档。 请参阅[添加或更新文档](#AddOrUpdateDocuments)了解此后续步骤。

有关在 Azure 搜索中编制索引的视频简介，请参阅[关于 Azure 搜索的第 9 频道 Cloud Cover 系列](http://go.microsoft.com/fwlink/p/?LinkId=511509)。

<a name="CreateIndex"></a>

## <a name="create-index"></a>创建索引
索引是在 Azure 搜索中组织和搜索文档的主要手段，类似于表如何在数据库中组织记录。 每个索引都具有全部符合索引架构（字段名称、数据类型和属性）的文档集合，但索引还指定用于定义其他搜索行为的其他构造（建议器、计分配置文件和 CORS 选项）。

可使用 HTTP POST 或 PUT 请求在 Azure 搜索服务内创建新索引。 请求正文是指定索引和配置信息的 JSON 架构。

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

此外，可使用 PUT 并在 URI 上指定索引名称。 如果索引不存在，将创建它。

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

创建索引确定在搜索操作中存储和使用的文档结构。 填充索引是单独的操作。 对于此步骤，可使用[索引器](https://msdn.microsoft.com/library/azure/mt183328.aspx)（可用于受支持的数据源）或[添加、更新或删除文档](https://msdn.microsoft.com/library/azure/dn798930.aspx)操作。 发布文档时，生成逆选索引。

**注意**：允许的索引最大数因定价层而异。 免费服务允许最多 3 个索引。 标准服务允许每个搜索服务有 50 个索引。 有关详细信息，请参阅[限制和约束](http://msdn.microsoft.com/library/azure/dn798934.aspx)。

**请求**

所有服务请求都需要 HTTPS。 **创建索引**请求可使用 POST 或 PUT 方法构造。 使用 POST 时，必须在请求正文中提供索引名称以及索引架构定义。 对于 PUT，索引名称是 URL 的一部分。 如果索引不存在，将创建它。 如果已经存在，则将其更新为新定义。

索引名称必须小写、以字母或数字开头、不包含斜杠或点，并且少于 128 个字符。 索引名称以字母或数字开头后，名称其余部分可包含任何字母、数字和短划线，前提是短划线不是连续的。

`api-version` 是必需的。 有关可用版本的列表，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `Content-Type`：必需。 将其设置为 `application/json`
* `api-key`：必需。 `api-key` 用于
* 对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **创建索引**请求必须包含一个设置为管理键（而不是查询键）的 `api-key` 标头。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

<a name="RequestData"></a>
**请求正文语法**

请求正文包含架构定义，其中包含将送入此索引的文档内的数据字段、数据类型、属性的列表以及用于在查询时为匹配文档计分的计分配置文件可选列表。

请注意，对于 POST 请求，必须在请求正文中指定索引名称。

索引中只能有一个关键字段。 它必须是字符串字段。 此字段表示存储在索引内的每个文档的唯一标识符。

索引的主要部分包括以下内容：

* `name`
* `fields`：将送入此索引，包括名称、数据类型和定义该字段上的允许操作的属性。
* `suggesters`：用于自动完成或提前键入查询。
* `scoringProfiles`：用于自定义搜索计分排名。 有关详细信息，请参阅[添加计分配置文件](https://msdn.microsoft.com/library/azure/dn798928.aspx)。
* `analyzers`、`charFilters`、`tokenizers`、`tokenFilters` 用于定义文档/查询如何分解为可索引/可搜索的标记。 有关详细信息，请参阅 [Azure 搜素中的分析](https://aka.ms//azsanalysis)。
* `defaultScoringProfile`：用于覆盖默认计分行为。
* `corsOptions`：允许针对索引的跨域查询。

构造请求负载的语法如下。 本主题中进一步提供了示例请求。

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
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
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

**索引属性**

可在创建索引时设置以下属性。 有关计分和计分配置文件的详细信息，请参阅[添加计分配置文件](https://msdn.microsoft.com/library/azure/dn798928.aspx)。

`name` - 设置字段的名称。

`type` - 设置字段的数据类型。

`searchable` - 将字段标记为全文可搜索。 这意味着它将在索引期间受到分词之类的分析。 如果将 `searchable` 字段设置为“sunny day”之类的值，在内部它将拆分为单独的标记“sunny”和“day”。 这实现了对这些词的全文搜素。 `Edm.String` 或 `Collection(Edm.String)` 类型的字段默认为 `searchable`。 其他类型的字段不能为 `searchable`。

* **注意**：`searchable` 字段在索引中消耗额外空间，因为 Azure 搜索将为全文搜索存储字段值的额外标记化版本。 如果要节省索引中的空间，并且不需要在搜索中包含字段，请将 `searchable` 设置为 `false`。

`filterable` - 允许在 `$filter` 查询中引用字段。 `filterable` 在处理字符串的方式方面与 `searchable` 不同。 `filterable` 的 `Edm.String` 或 `Collection(Edm.String)` 类型的字段不进行分词，因此比较仅用于完全匹配。 例如，如果将此类字段 `f` 设置为“sunny day”，`$filter=f eq 'sunny'` 将找不到任何匹配，但 `$filter=f eq 'sunny day'` 可找到。 默认情况下，所有字段均为 `filterable`。

`sortable` - 默认情况下，系统按计分为结果排序，但在许多情况下，用户将希望按文档中的字段排序。 `Collection(Edm.String)` 类型的字段不能为 `sortable`。 默认情况下，所有其他字段均为 `sortable`。

`facetable`- 通常在按类别包含命中次数的搜索结果展示中使用（例如，搜索数码相机并按品牌、像素、价格等查看命中）。 此选项无法与 `Edm.GeographyPoint` 类型的字段一起使用。 默认情况下，所有其他字段均为 `facetable`。

* **注意**：`filterable`、`sortable` 或 `facetable` 的 `Edm.String` 类型的字段最长可以为 32KB。 这是因为此类字段视为单个搜索词，而 Azure 搜索中一个词的最大长度为 32KB。 如果需要在单个字符串字段中存储多于此大小的文本，将需要在索引定义中将 `filterable`、`sortable` 和 `facetable` 显式设置为 `false`。
* **注意**：如果字段未将上述任何属性设置为 `true`（`searchable`、`filterable`、`sortable` 或`facetable`），则将该字段从逆选索引中有效排除。 此选项对不在查询中使用但在搜索结果中需要的字段很有用。 从索引中排除此类字段可提升性能。

`key` - 将字段标记为索引内的文档的唯一标识符。 必须恰好有一个字段选作 `key` 字段，并且它必须属于 `Edm.String` 类型。 关键字段可通过[查找 API](#LookupAPI) 直接查找文档。

`retrievable` - 设置字段是否可在搜索结果中返回。  当要将字段（例如利润）用作筛选器、排序或计分机制，但不希望该字段对最终用户可见时，这很有用。 对于 `key` 字段，此属性必须为 `true`。

`analyzer` - 设置要在搜索时和索引时用于字段的分析器的名称。 对于允许的值集，请参阅[分析器](https://msdn.microsoft.com/library/mt605304.aspx)。 此选项只能用于 `searchable` 字段，并且无法与 `searchAnalyzer` 或 `indexAnalyzer` 一起设置。  选择分析器后，无法为字段更改它。

`searchAnalyzer` - 设置在搜索时为字段使用的分析器的名称。 对于允许的值集，请参阅[分析器](https://msdn.microsoft.com/library/mt605304.aspx)。 此选项只能用于 `searchable` 字段。 它必须与 `indexAnalyzer` 一起设置，并且无法与 `analyzer` 选项一起设置。 此分析器无法在现有字段上更新。

`indexAnalyzer` - 设置在索引时为字段使用的分析器的名称。 对于允许的值集，请参阅[分析器](https://msdn.microsoft.com/library/mt605304.aspx)。 此选项只能用于 `searchable` 字段。 它必须与 `searchAnalyzer` 一起设置，并且无法与 `analyzer` 选项一起设置。 选择分析器后，无法为字段更改它。

`suggesters` - 设置搜索模式和作为建议内容源的字段。 有关详细信息，请参阅[建议器](#Suggesters)。

`scoringProfiles` - 定义自定义计分行为，可使你影响哪些项目在搜索结果显示得更高。 计分配置文件由字段权重和函数组成。 有关计分配置文件中使用的属性，请参阅[添加计分配置文件](https://msdn.microsoft.com/library/azure/dn798928.aspx)。

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**语言支持**

可搜索字段进行分析，这些分析通常涉及到分词、文本规范化和筛选出词。 默认情况下，Azure 搜索中的可搜索字段使用 [Apache Lucene 标准分析器](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html)分析，该分析器按照[“Unicode 文本分段”](http://unicode.org/reports/tr29/)规则将文本分解为多个元素。 此外，标准分析器将所有字符转换为其小写形式。 已编入索引的文档和搜索词在索引和查询处理期间完成分析。

Azure 搜索支持各种不同的语言。 每种语言都需要考虑给定语言特征的非标准文本分析器。 Azure 搜索提供两种类型的分析器：

* Lucene 支持的 35 个分析器。
* Office 和必应中使用的专有 Microsoft 自然语言处理技术支持的 50 个分析器。

某些开发人员可能首选更熟悉、简单的开源 Lucene 解决方案。 Lucene 分析器更快，但 Microsoft 分析器具有高级功能，如词形还原、字词分解（在德语、丹麦语、荷兰语、瑞典语、挪威语、爱沙尼亚语、芬兰语、匈牙利语、斯洛伐克语中）和实体识别（URL、电子邮件、日期、数字）。 如果可能，应对 Microsoft 和 Lucene 分析器进行比较以确定哪一个更合适。

***它们之间的比较***

适用于英语的 Lucene 分析器扩展了标准分析器。 它从字词中删除所有格（尾部的 's）、根据 [Porter 词干分解算法](http://tartarus.org/~martin/PorterStemmer/)应用词干分解，并删除英语[非索引字](http://en.wikipedia.org/wiki/Stop_words)。

相比之下，Microsoft 分析器执行词形还原，而不是词干分解。 这意味着它可以更好地处理曲折和不规则字词形式，从而产生相关度更高的搜索结果（有关更多详细信息，请观看 [Azure 搜索 MVA 演示](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps)的模块 7）。

Microsoft 分析器的索引平均比 Lucene 的索引慢两到三倍，具体取决于语言。 对于平均大小的查询，搜索性能应该不会受到显著影响。

***配置***

对于索引定义中的每个字段，可将 `analyzer` 属性设置为指定哪个语言和供应商的分析器名称。 将在为该字段编入索引和搜索时应用相同的分析器。
例如，可以为在同一个索引中并行存在的英语、法语和西班牙语酒店说明使用单独的字段。 使用 ['searchFields' 查询参数](#SearchQueryParameters)指定在查询中针对哪个特定于语言的字段进行搜索。 可在[搜索文档](#SearchDocs)中查看包含 `analyzer` 属性的查询示例。 

***分析器列表***

下面是受支持语言的列表以及 Lucene 和 Microsoft 分析器名称。

<table style="font-size:12">
    <tr>
        <th>语言</th>
        <th>Microsoft 分析器名称</th>
        <th>Lucene 分析器名称</th>
    </tr>
    <tr>
        <td>阿拉伯语</td>
        <td>ar.microsoft</td>
        <td>ar.lucene</td>        
    </tr>
    <tr>
        <td>亚美尼亚语</td>
        <td></td>
        <td>hy.lucene</td>
      </tr>
    <tr>
        <td>Bangla</td>
        <td>bn.microsoft</td>
        <td></td>
    </tr>
      <tr>
        <td>巴斯克语</td>
        <td></td>
        <td>eu.lucene</td>
    </tr>
      <tr>
         <td>保加利亚语</td>
        <td>bg.microsoft</td>
        <td>bg.lucene</td>
      </tr>
      <tr>
        <td>加泰罗尼亚语</td>
        <td>ca.microsoft</td>
        <td>ca.lucene</td>          
      </tr>
    <tr>
        <td>中文(简体)</td>
        <td>zh-Hans.microsoft</td>
        <td>zh-Hans.lucene</td>        
    </tr>
    <tr>
        <td>中文(繁体)</td>
        <td>zh-Hant.microsoft</td>
        <td>zh-Hant.lucene</td>        
    <tr>
    <tr>
        <td>克罗地亚语</td>
        <td>hr.microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>捷克语</td>
        <td>cs.microsoft</td>
        <td>cs.lucene</td>        
    </tr>    
    <tr>
        <td>丹麦语</td>
        <td>da.microsoft</td>
        <td>da.lucene</td>        
    </tr>    
    <tr>
        <td>荷兰语</td>
        <td>nl.microsoft</td>
        <td>nl.lucene</td>    
    </tr>    
    <tr>
        <td>英语</td>        
        <td>en.microsoft</td>
        <td>en.lucene</td>        
    </tr>
    <tr>
        <td>爱沙尼亚语</td>
        <td>et.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>芬兰语</td>
        <td>fi.microsoft</td>
        <td>fi.lucene</td>        
    </tr>    
    <tr>
        <td>法语</td>
        <td>fr.microsoft</td>
        <td>fr.lucene</td>        
    </tr>
    <tr>
        <td>加利西亚语</td>
        <td></td>
        <td>gl.lucene</td>        
      </tr>
    <tr>
        <td>德语</td>
        <td>de.microsoft</td>
        <td>de.lucene</td>        
    </tr>
    <tr>
        <td>希腊语</td>
        <td>el.microsoft</td>
        <td>el.lucene</td>        
    </tr>
    <tr>
        <td>古吉拉特语</td>
        <td>gu.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>希伯来语</td>
        <td>he.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>印地语</td>
        <td>hi.microsoft</td>
        <td>hi.lucene</td>        
    </tr>
    <tr>
        <td>匈牙利语</td>        
        <td>hu.microsoft</td>
        <td>hu.lucene</td>
    </tr>
    <tr>
        <td>冰岛语</td>
        <td>is.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>印度尼西亚语</td>
        <td>id.microsoft</td>
        <td>id.lucene</td>        
    </tr>
    <tr>
        <td>爱尔兰语</td>
        <td></td>
          <td>ga.lucene</td>
    </tr>
    <tr>
        <td>意大利语</td>
        <td>it.microsoft</td>
        <td>it.lucene</td>        
    </tr>
    <tr>
        <td>日语</td>
        <td>ja.microsoft</td>
        <td>ja.lucene</td>

    </tr>
    <tr>
        <td>卡纳达语</td>
        <td>ka.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>朝鲜语</td>
        <td>ko.microsoft</td>
        <td>ko.lucene</td>
    </tr>
    <tr>
        <td>拉脱维亚语</td>        
        <td>lv.microsoft</td>
        <td>lv.lucene</td>    
    </tr>
    <tr>
        <td>立陶宛语</td>
        <td>lt.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>马拉雅拉姆语</td>
        <td>ml.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>马来语(拉丁语系)</td>
        <td>ms.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>马拉地语</td>
        <td>mr.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>挪威语</td>
        <td>nb.microsoft</td>
        <td>no.lucene</td>        
    </tr>
      <tr>
        <td>波斯语</td>
        <td></td>
        <td>fa.lucene</td>        
      </tr>
    <tr>
        <td>波兰语</td>
        <td>pl.microsoft</td>
        <td>pl.lucene</td>        
    </tr>
    <tr>
        <td>葡萄牙语(巴西)</td>
        <td>pt-Br.microsoft</td>
        <td>pt-Br.lucene</td>        
    </tr>
    <tr>
        <td>葡萄牙语(葡萄牙)</td>
        <td>pt-Pt.microsoft</td>        
        <td>pt-Pt.lucene</td>
    </tr>
    <tr>
        <td>旁遮普语</td>
        <td>pa.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>罗马尼亚语</td>
        <td>ro.microsoft</td>
        <td>ro.lucene</td>
    </tr>
    <tr>
        <td>俄语</td>
        <td>ru.microsoft</td>
        <td>ru.lucene</td>    
    </tr>
    <tr>
        <td>塞尔维亚语(西里尔文)</td>
        <td>sr-cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>塞尔维亚语(拉丁语系)</td>
        <td>sr-latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>斯洛伐克语</td>
        <td>sk.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>斯洛文尼亚语</td>
        <td>sl.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>西班牙语</td>
        <td>es.microsoft</td>
        <td>es.lucene</td>
    </tr>
    <tr>
        <td>瑞典语</td>
        <td>sv.microsoft</td>
        <td>sv.lucene</td>
    </tr>

    <tr>
        <td>泰米尔语</td>
        <td>ta.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>泰卢固语</td>
        <td>te.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>泰语</td>
        <td>th.microsoft</td>
        <td>th.lucene</td>
    </tr>
    <tr>
        <td>土耳其语</td>
        <td>tr.microsoft</td>
        <td>tr.lucene</td>        
    </tr>
    <tr>
        <td>乌克兰语</td>
        <td>uk.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>乌尔都语</td>
        <td>ur.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>越南语</td>
        <td>vi.microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">此外，Azure 搜索还提供与语言无关的分析器配置</td>
    <tr>
        <td>标准 ASCII 折叠</td>
        <td>standardasciifolding.lucene</td>
        <td>
        <ul>
            <li>Unicode 文本分段（标准标记化器）</li>
            <li>ASCII 折叠筛选器 - 将不属于前 127 个 ASCII 字符集的 Unicode 字符转换为其 ASCII 等效字符。 这对于删除读音符号很有用。</li>
        </ul>
        </td>
    </tr>
</table>

名称带有 <i>lucene</i> 批注的所有分析器都由 [Apache Lucene 的语言分析器](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html)提供支持。 可在[此处](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)找到有关 ASCII 折叠筛选器的详细信息。

**建议器**

`suggester` 定义索引中的哪些字段用于支持搜索中的自动完成。 当用户尝试键入搜索查询时，通常将部分搜索字符串发送到[建议 API](#Suggestions)，然后该 API 返回一组建议的短语。 在索引中定义的建议器确定哪些字段用于生成提前键入的搜索词。 有关配置详细信息，请参阅[建议器](#Suggesters)。

**为配置文件评分**

`scoringProfile` 定义自定义计分行为，可使你影响哪些项目在搜索结果显示得更高。 计分配置文件由字段权重和函数组成。 若要使用它们，请在查询字符串上按名称指定配置文件。

在幕后运行的默认计分概要文件，用于为结果集中的每个项目计算搜索分数。 可使用内部、未命名的计分概要文件。 或者，将 `defaultScoringProfile` 设置为使用自定义配置文件作为默认配置文件，每当未在查询字符串上指定自定义配置文件时，则调用该配置文件。

有关详细信息，请参阅[将计分配置文件添加到搜索索引（Azure 搜索服务 REST API）](search-api-scoring-profiles-2015-02-28-preview.md)。

**CORS 选项**

默认情况下，客户端 Javascript 无法调用任何 API，因为浏览器将阻止所有跨域请求。 通过将 `corsOptions` 属性设置为允许对索引的跨域请求来启用 CORS（跨域资源共享）。 请注意，出于安全原因，仅查询 API 支持 CORS。 可为 CORS 设置以下选项：

* `allowedOrigins`（必需）：这是将被授予索引访问权限的来源的列表。 这意味着，将允许从这些来源提供的任何 Javascript 代码查询索引（假设它提供正确的 API 密钥）。 每个来源通常都采用 `protocol://fully-qualified-domain-name:port` 形式，尽管通常省略端口。 请参阅[本文](http://go.microsoft.com/fwlink/?LinkId=330822)了解更多详细信息。
  * 如果要允许对所有来源的访问，请将 `*` 作为单个项目包含在 `allowedOrigins` 数组中。 请注意，**对于生产搜索服务，不推荐此做法。** 但是，对于开发或调试目的，它可能很有用。
* `maxAgeInSeconds`（可选）：浏览器使用此值确定缓存 CORS 预检响应的持续时间（以秒为单位）。 此值必须是非负整数。 此值越大，性能越好，但 CORS 策略更改生效所需的时间也越长。 如果未设置此值，将使用 5 分钟的默认持续时间。

<a name="CreateUpdateIndexExample"></a>
**请求正文示例**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**响应**

对于成功的请求：“201 Created”。

默认情况下，响应正文将包含已创建的索引定义的 JSON。 如果 `Prefer` 请求标头设置为 `return=minimal`，响应正文将为空，并且成功状态代码将是“204 No Content”，而不是“201 Created”。 无论使用 PUT 还是 POST 创建索引都是如此。

**备注**

当前，对索引架构更新的支持有限。 当前不支持任何需要重新编入索引的架构更新（如更改字段类型）。 尽管无法更改或删除现有字段，但可随时将新字段添加到现有索引。 添加新字段时，索引中的所有现有文档都将自动为该字段采用 null 值。 在向索引添加新文档之前，将不使用任何额外的存储空间。

<a name="Suggesters"></a>

## <a name="suggesters"></a>建议器
Azure 搜索中的建议功能是提前键入或自动完成查询功能，提供潜在的搜索词列表，以响应在搜索框中输入的部分字符串输入。 你可能已注意到使用商业 Web 搜索引擎时的查询建议：在必应中键入“.NET”会生成“.NET 4.5”、“.NET Framework 3.5”等的术语列表。 使用搜索服务 REST API 时，在自定义 Azure 搜索应用程序中实现建议需要以下操作：

* 启用建议，方法是在索引中添加**建议器**构造，提供名称、搜索模式和为其调用提前键入的字段的列表。 例如，如果指定“cityName”作为源字段，则键入部分搜索字符串“Seattle”将导致向用户提供“Seattle”、“Seaside”和“Seatac”（三个都是实际的城市名称）作为查询建议。
* 通过在应用程序代码中调用[建议 API](#Suggestions) 来调用建议。 当用户键入搜索查询时，通常将部分搜索字符串发送到服务，然后此 API 返回一组建议的短语。

本文介绍如何配置**建议器**。 还应查看[建议 API](#Suggestions) 了解有关如何使用建议器的详细信息。

**使用情况**

`Suggesters` 在索引中创建，并且在用于建议特定文档（而不是松散的字词或短语）时效果最佳。 最佳的候选字段是标题、名称和其他可标识某个项目的相对较短的短语。 效果较差的是重复性字段（如类别和标记）或非常长的字段（如说明或评论字段）。

作为索引定义的一部分，可将单个建议器添加到 `suggesters` 集合。 定义建议器的属性包括以下内容：

* `name`：建议器的名称。 调用 `suggest` API 时使用建议器的名称。
* `searchMode`：用于搜索候选短语的策略。 当前唯一受支持的模式是 `analyzingInfixMatching`，此模式在句子开头或中间执行短语的灵活匹配。
* `sourceFields`：作为建议内容源的一个或多个字段的列表。 只有 `Edm.String` 和 `Collection(Edm.String)` 类型的字段可作为建议的源。 只能使用没有自定义语言分析器的字段。

**建议器示例**

建议器是索引的一部分。 在当前版本中，在字段集合和 `scoringProfiles` 旁边，`suggesters` 集合中只能存在一个建议器。

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [!NOTE]
> 如果使用了 Azure 搜索的公共预览版，`suggesters` 将替换较早的布尔属性 (`"suggestions": false`)，该属性仅支持短字符串（3-25 个字符）的前缀建议。 它的替代物 `suggesters` 支持中缀匹配，此匹配在字段内容的开头或中间查找匹配字词，并且能够更好地容忍搜索字符串中的错误。 从公开发布的版本开始，这现在是建议 API 的唯一实现。 `api-version=2014-07-31-Preview` 中引入的早期 `suggestions` 属性在该版本中继续工作，但在 `2015-02-28` 或更新版本 Azure 搜索中不可操作。
> 
> 

<a name="UpdateIndex"></a>

## <a name="update-index"></a>更新索引
可使用 HTTP PUT 请求更新 Azure 搜索内的现有索引。 更新可包括将新字段添加到现有架构、修改 CORS 选项和修改计分配置文件。 有关详细信息，请参阅[添加计分配置文件](https://msdn.microsoft.com/library/azure/dn798928.aspx)。 指定要在请求 URI 上更新的索引的名称：

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**重要提示：**对索引架构更新的支持仅限于不需要重新生成搜索索引的操作。 当前不支持任何需要重新编入索引的架构更新（如更改字段类型）。 尽管无法更改或删除现有字段，但可以随时添加新字段。 这同样适用于 `suggesters`。 可在添加字段时将新字段添加到建议器，但无法从 `suggesters` 删除字段，并且无法将现有字段添加到 `suggesters`。

向索引添加新字段时，索引中的所有现有文档都将自动为该字段采用 null 值。 在向索引添加新文档之前，将不使用任何额外的存储空间。

**请求**

所有服务请求都需要 HTTPS。 使用 HTTP PUT 构造**更新索引**请求。 对于 PUT，索引名称是 URL 的一部分。 如果索引不存在，将创建它。 如果索引已经存在，则将其更新为新定义。

索引名称必须小写、以字母或数字开头、不包含斜杠或点，并且少于 128 个字符。 索引名称以字母或数字开头后，名称其余部分可包含任何字母、数字和短划线，前提是短划线不是连续的。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `Content-Type`：必需。 将其设置为 `application/json`
* `api-key`：必需。 `api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **更新索引**请求必须包含一个设置为管理键（而不是查询键）的 `api-key` 标头。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文语法**

更新现有索引时，正文必需包含原始架构定义加上要添加的新字段，以及已修改的计分配置文件、建议器和 CORS 选项（如果有）。 如果不修改计分配置文件和 CORS 选项，则必须从创建索引时开始包含来源。 一般情况下，用于更新的最佳模式是使用 GET 检索索引定义、修改它，然后使用 PUT 更新它。

为方便起见，此处重现用于创建索引的架构语法。 有关更多详细信息，请参阅[创建索引](#CreateIndex)。

    {
      "name": (optional) "name_of_index",
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
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
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


**响应**

对于成功的请求：“204 No Content”。

默认情况下，响应正文将为空。 但是，如果 `Prefer` 请求标头设置为 `return=representation`，响应正文将包含已更新的索引定义的 JSON。 在此情况下，成功状态代码将是“200 正常”。

**使用自定义分析器更新索引定义**

定义分析器、标记化器、标记筛选器或字符筛选器后，便无法修改它。 仅当 `allowIndexDowntime` 标志在索引更新请求中设置为 true 时，才可向现有索引添加新的上述内容： 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

请注意，此操作将使索引离线至少几秒钟，从而导致索引和查询请求失败。 索引的性能和写入可用性可在更新索引后的几分钟内处于受损状态，对于非常大的索引，持续时间更长。

<a name="ListIndexes"></a>

## <a name="list-indexes"></a>列出索引
**列出索引**操作返回当前在 Azure 搜索服务中的索引列表。

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**请求**

所有服务请求都需要 HTTPS。 可使用 GET 方法构造**列出索引**请求。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `api-key`：必需。 `api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **列出索引**请求必须包含一个设置为管理键（而不是查询键）的 `api-key`。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

无。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

下面是响应正文示例：

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

请注意，可仅将响应筛选为感兴趣的属性。 例如，如果只需要索引名称的列表，请使用 OData `$select` 查询选项：

    GET /indexes?api-version=2015-02-28-Preview&$select=name

在此情况下，来自上述示例的响应将如下所示：

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

如果在搜索服务中有大量索引，这是节省带宽的有用技术。

<a name="GetIndex"></a>

## <a name="get-index"></a>获取索引
**获取索引**操作从 Azure 搜索获取索引定义。

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**请求**

对于服务请求，需要 HTTPS。 可使用 GET 方法构造**获取索引**请求。

请求 URI 中的 [index name] 指定要从索引集合返回哪个索引。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `api-key`：`api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **获取索引**请求必须包含一个设置为管理键（而不是查询键）的 `api-key`。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

无。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

有关响应有效负载的示例，请参阅[创建和更新索引](#CreateUpdateIndexExample)中的 JSON。

<a name="DeleteIndex"></a>

## <a name="delete-index"></a>删除索引
**删除索引**从 Azure 搜索服务中删除索引和相关联的文档。 可从 Azure 门户中的服务仪表板或 API 获取索引名称。 有关详细信息，请参阅[列出索引](#ListIndexes)。

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**请求**

对于服务请求，需要 HTTPS。 可使用 DELETE 方法构造**删除索引**请求。

请求 URI 中的 [index name] 指定要从索引集合中删除哪个索引。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `api-key`：必需。 `api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务 URL 是唯一的。 **删除索引**请求必须包含一个设置为管理键（而不是查询键）的 `api-key` 标头。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

无。

**响应**

对于成功的响应，返回“状态代码：204 无内容”。

<a name="GetIndexStats"></a>

## <a name="get-index-statistics"></a>获取索引统计信息
**获取索引统计信息**操作从 Azure 搜索返回当前索引的文档计数以及存储使用情况。

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> 每隔几分钟收集关于文档计数和存储大小的统计信息，而非实时收集。 因此，此 API 返回的统计信息可能不反映最近的索引操作导致的更改。
> 
> 

**请求**

所有服务请求都需要 HTTPS。 可使用 GET 方法构造**获取索引统计信息**请求。

请求 URI 中的 [index name] 告知服务返回指定索引的索引统计信息。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `api-key`：`api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **获取索引统计信息**请求必须包含一个设置为管理键（而不是查询键）的 `api-key`。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

无。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

响应正文格式如下：

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>

## <a name="test-analyzer"></a>测试分析器
**分析器 API** 显示分析器如何将文本分解为标记。

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**请求**

所有服务请求都需要 HTTPS。 可使用 POST 方法构造**分析 API** 请求。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `api-key`：`api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **分析 API** 请求必须包含一个设置为管理键（而不是查询键）的 `api-key`。

还需要索引名称和服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

或

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

`analyzer_name`、`tokenizer_name`、`token_filter_name` 和 `char_filter_name` 需要是索引的预定义或自定义分析器、标记化器、标记筛选器和字符筛选器的有效名称。 若要了解有关词法分析过程的详细信息，请参阅 [Azure 搜索中的分析](https://aka.ms/azsanalysis)。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

响应正文格式如下：

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**分析 API 示例**

**请求**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**响应**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

- - -
<a name="DocOps"></a>

## <a name="document-operations"></a>文档操作
在 Azure 搜索中，索引存储在云中，并使用上传到服务的 JSON 文档填充。 上传的所有文档构成了搜索数据的语料库。 文档包含字段，其中某些字段在上传时标记化为搜索词。 Azure 搜索 API 中的 `/docs` URL 段表示索引中的文档集合。 对集合执行的所有操作（如上传、合并、删除或查询文档）都发生在单个索引的上下文中，因此这些操作的 URL 对于给定索引名称将始终以 `/indexes/[index name]/docs` 开头。

应用程序代码必须生成 JSON 文档才能上传到 Azure 搜索，或者，如果数据源是 Azure SQL 数据库或 Azure Cosmos DB，则可以使用[索引器](https://msdn.microsoft.com/library/dn946891.aspx)加载文档。 通常，从提供的单个数据集填充索引。

应计划为要搜索的每个项目使用一个文档。 电影租赁应用程序可能为每部电影使用一个文档，店铺应用程序可能为每个 SKU 使用一个文档，在线课件应用程序可能为每个课程使用一个文档，研究公司可能为其存储库中的每个学术论文使用一个文档，等等。

文档由一个或多个字段组成。 字段可包含由 Azure 搜索标记化为搜索词的文本，以及可在筛选器或计分配置文件中使用的非标记化或非文本值。 每个字段支持的名称、数据类型和搜索功能取决于索引架构。 必须将每个索引架构中的字段之一指定为 ID，并且每个文档必须为在索引中唯一标识该文档的 ID 字段提供一个值。 所有其他文档字段均为可选，如果未指定，则默认为 null 值。 请注意，null 值不在搜索索引中占用空间。

必须先在服务上创建索引，才能上传文档。 有关此先行步骤的详细信息，请参阅[创建索引](#CreateIndex)。

<a name="AddOrUpdateDocuments"></a>

## <a name="add-update-or-delete-documents"></a>添加、更新或删除文档
可使用 HTTP POST 从指定的索引上传、合并、合并或上传或删除文档。 对于大量更新，建议使用文档的批处理（每批最多 1000 个文档或每批大约 16 MB）。

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**请求**

所有服务请求都需要 HTTPS。 可使用 HTTP POST 从指定的索引上传、合并、合并或上传或删除文档。

请求 URI 包含 [index name]，用于指定要发布文档的索引。 一次只能将文档发布到一个索引。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `Content-Type`：必需。 将其设置为 `application/json`
* `api-key`：必需。 `api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **添加文档**请求必须包含一个设置为管理键（而不是管理键）的 `api-key` 标头。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

请求的正文包含要编入索引的一个或多个文档。 文档由唯一键标识。 每个文档与某个操作相关联：上传、合并、合并或上传，或者删除。 上传请求必须包含文档数据作为一组键/值对。

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [!NOTE]
> 文档键只能包含字母、数字、破折号 ("-")、下划线 ("_") 和等号 ("=")。 有关详细信息，请参阅[命名规则](https://msdn.microsoft.com/library/azure/dn857353.aspx)。
> 
> 

**文档操作**

* `upload`：上传操作类似于“upsert”，如果文档是新文档，则插入；如果文档已经存在，则进行更新/替换。 请注意，在更新的情况下，替换所有字段。
* `merge`：Merge 使用指定的字段更新现有文档。 如果文档不存在，合并将失败。 merge 中指定的任何字段都将替换文档中的现有字段。 包括 `Collection(Edm.String)`类型的字段。 例如，如果文档包含值为 `["budget"]` 的字段“tags”，并且已使用值 `["economy", "pool"]` 对“tags”执行合并，则“tags”字段的最终值将为 `["economy", "pool"]`。 它**不会**是 `["budget", "economy", "pool"]`。
* `mergeOrUpload`：如果索引中已存在具有给定关键字段的文档，则此操作的行为类似于 `merge`。 如果该文档不存在，则它的行为类似于对新文档进行 `upload`。
* `delete`：Delete 从索引中删除指定文档。 请注意，除关键字段外，在 `delete` 操作中指定的所有字段都将被忽略。 如果要从文档中删除个别字段，请改用 `merge`，只需将该字段显式设置为 `null`。

**响应**

对于成功的响应，返回状态代码 200 (正常)，这意味着所有项目都已成功编入索引。 这通过 `status` 属性对所有项目设置为 true 以及 `statusCode` 属性设置为 201（对新上传的文档）或 200（对合并或删除的文档）来指示：

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

当至少有一个项目未成功编入索引时，返回状态代码 207 (Multi-Status)。 未编入索引的项目将 `status` 字段设置为 false。 `errorMessage` 和 `statusCode` 属性将指示索引错误的原因：

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

下表说明可在响应中返回的各种每文档状态代码。 请注意，某些代码指示请求本身的问题，而其他代码指示临时错误条件。 对于后者，应在延迟后重试。

<table style="font-size:12">
    <tr>
        <th>状态代码</th>
        <th>含义</th>
        <th>可重试</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>200</td>
        <td>文档已成功修改或删除。</td>
        <td>不适用</td>
        <td>删除操作是<a href="https://en.wikipedia.org/wiki/Idempotence">幂等的</a>。 即，即使索引中不存在文档键，使用该键尝试执行删除操作仍将导致 200 状态代码。</td>
    </tr>
    <tr>
        <td>201</td>
        <td>已成功创建文档。</td>
        <td>不适用</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>文档中存在阻止其编入索引的错误。</td>
        <td>否</td>
        <td>响应中的错误消息将指示文档存在的问题。</td>
    </tr>
    <tr>
        <td>404</td>
        <td>文档无法合并，因为索引中不存在给定键。</td>
        <td>否</td>
        <td>上传不会发生此错误，因为它们创建新文档，删除也不会发生此错误，因为它们是<a href="https://en.wikipedia.org/wiki/Idempotence">幂等的</a>。</td>
    </tr>
    <tr>
        <td>409</td>
        <td>尝试将文档编入索引时检测到了版本冲突。</td>
        <td>是</td>
        <td>尝试将相同文档同时多次编入索引时，可能发生此错误。</td>
    </tr>
    <tr>
        <td>422</td>
        <td>索引暂时不可用，因为在将“allowIndexDowntime”标志设置为“true”的情况下更新了它。</td>
        <td>是</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>搜索服务暂时不可用，可能是因为负荷较重。</td>
        <td>是</td>
        <td>在此情况下，代码应在重试前等待，否则面临延长服务不可用性状态的风险。</td>
    </tr>
</table> 

**注意**：如果客户端代码经常遇到 207 响应，一个可能的原因是系统承受负载。 可通过检查 `statusCode` 属性中的 503 来确定此原因。 如果是这种情况，我们建议***限制索引请求***。 否则，如果索引流量不下降，系统可能开始使用 503 错误拒绝所有请求。

状态代码 429 指示已超出每个索引的文档数配额。 必须创建新索引或升级以提高容量限制。

**示例：**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
- - -
<a name="SearchDocs"></a>

## <a name="search-documents"></a>搜索文档
**搜索**操作作为 GET 或 POST 请求发出，并指定为选择匹配文档提供标准的参数。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**何时使用 POST 而不是 GET**

使用 HTTP GET 调用**搜索** API 时，需要了解请求 URL 的长度不能超过 8 KB。 对于大多数应用程序，这通常已足够。 但是，某些应用程序产生非常大的查询或 OData 筛选器表达式。 对于这些应用程序，使用 HTTP POST 是更好的选择，因为它允许比 GET 更大的筛选器和查询。 对于 POST，限制因素是查询中词或子句的数量，而不是原始查询的大小，因为 POST 的请求大小限制大约为 16 MB。

> [!NOTE]
> 即使 POST 请求大小限制非常大，搜索查询和筛选器表达式也不能任意复杂。 有关搜索查询和筛选器复杂性限制的详细信息，请参阅 [Lucene 查询语法](https://msdn.microsoft.com/library/mt589323.aspx)和 [OData 表达式语法](https://msdn.microsoft.com/library/dn798921.aspx)。
> 
> 

**请求**

对于服务请求，需要 HTTPS。 可使用 GET 或 POST 方法构造**搜索**请求。

对于与参数匹配的所有文档，请求 URI 指定要查询哪个索引。 对于 GET 请求，参数在查询字符串上指定，对于 POST 请求，参数在请求正文中指定。

作为创建 GET 请求时的最佳做法，请记得在直接调用 REST API 时对特定查询参数进行 [URL 编码](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx)。 对于**搜索**操作，这包括：

* `$filter`
* `facet`
* `highlightPreTag`
* `highlightPostTag`
* `search`
* `moreLikeThis`

仅对上述查询参数建议使用 URL 编码。 如果无意中对整个查询字符串（? 后的所有内容）进行 URL 编码，请求将中断。

此外，仅在使用 GET 直接调用 REST API 时，必须进行 URL 编码。 使用 POST 调用**搜索**或使用 [.NET 客户端库](https://msdn.microsoft.com/library/dn951165.aspx)（为你处理 URL 编码）时，不需要 URL 编码。

<a name="SearchQueryParameters"></a>
**查询参数**

**搜索**接受多个参数，这些参数提供查询标准，还指定搜索行为。 通过 GET 调用**搜索**时，在 URL 查询字符串中提供这些属性，当通过 POST 调用**搜索**时，提供这些参数作为请求正文中的 JSON 属性。 在 GET 和 POST 之间，某些参数的语法稍有不同。 这些差异在下面注明为适用：

`search=[string]`（可选）- 要搜索的文本。 除非指定了 `searchFields`，否则默认搜索所有 `searchable` 字段。 搜索 `searchable` 字段时，搜索文本本身已标记化，因此可通过空格分隔多个词（例如：`search=hello world`）。 若要匹配任何字词，请使用 `*`（这对布尔筛选器查询可能很有用）。 忽略此参数与将其设置为 `*` 具有相同的效果。 有关搜索语法的详细信息，请参阅[简单查询语法](https://msdn.microsoft.com/library/dn798920.aspx)。

* **注意**：当通过 `searchable` 字段查询时，结果有时可能令人惊讶。 标记化器包含处理英语文本中常见情况的逻辑，如撇号、数字中的逗号等。例如，`search=123,456` 将与单个字词 123,456 而不是个别字词 123 和 456 匹配，因为逗号在英语中用作大数的千位分隔符。 出于此原因，我们建议使用空格而不是标点符号分隔 `search` 参数中的字词。

`searchMode=any|all`（可选，默认为 `any`）- 若要将文档视为匹配，必须匹配任意一个还是全部搜索词。

`searchFields=[string]`（可选）- 要搜索指定文本的逗号分隔字段名称的列表。 目标字段必须标记为 `searchable`。

`queryType=simple|full`（可选，默认为 `simple`）- 当设置为“简单”时，使用简单查询语言解释搜索文本，该语言允许 +、* 和 "" 之类的符号。 默认情况下，在每个文档中的所有可搜索字段（或 `searchFields` 中指示的字段）中评估查询。 当查询类型设置为 `full` 时，使用 Lucene 查询语言解释搜索文本，该语言允许特定于字段和加权的搜索。 有关搜索语法的详细信息，请参阅[简单查询语法](https://msdn.microsoft.com/library/dn798920.aspx)和 [Lucene 查询语法](https://msdn.microsoft.com/library/mt589323.aspx)。 

> [!NOTE]
> 不支持使用 Lucene 查询语言的范围查询，但支持提供类似功能的 $filter。
> 
> 

`moreLikeThis=[key]`（可选）**重要说明：**此功能仅在 `2015-02-28-Preview` 中可用。 此选项无法在包含文本搜索参数 `search=[string]` 的查询中使用。 `moreLikeThis` 参数查找与文档键所指定文档类似的文档。 使用 `moreLikeThis` 发出搜索请求时，基于源文档中字词的频率和罕见程度生成搜索词列表。 然后，使用这些字词发出请求。 默认情况下，考虑所有 `searchable` 字段的内容，除非 `searchFields` 用于限制搜索哪些字段。  

`$skip=#`（可选）- 要跳过的搜索结果数；不能大于 100000。 如果需要按顺序扫描文档，但由于此限制而无法使用 `$skip`，请考虑在完全排序的键上使用 `$orderby`，并将 `$filter` 与范围搜索一起使用。

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `skip`，而不是 `$skip`。
> 
> 

`$top=#`（可选）- 要检索的搜索结果数。 这可以与 `$skip` 结合使用以实现搜索结果的客户端分页。

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `top`，而不是 `$top`。
> 
> 

`$count=true|false`（可选，默认为 `false`）- 指定是否要提取结果总数。 这是与 `search` 和 `$filter` 参数匹配的所有文档的计数，忽略 `$top` 和 `$skip`。 将此值设置为 `true` 可能影响性能。 请注意，返回的计数是一个近似值。

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `count`，而不是 `$count`。
> 
> 

`$orderby=[string]`（可选）- 为结果排序所依据的逗号分隔表达式的列表。 每个表达式可以是字段名称或对 `geo.distance()` 函数的调用。 每个表达式可以后跟 `asc` 来指示升序，后跟 `desc` 来指示降序。 默认值为升序。 排序的依据将是文档的匹配分数。 如果未指定 `$orderby`，默认排序顺序将为按文档匹配分数的降序。 对于 `$orderby`，有 32 个子句的限制。

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `orderby`，而不是 `$orderby`。
> 
> 

`$select=[string]`（可选）- 要检索的字段的逗号分隔列表。 如果未指定，将包含架构中标记为可检索的所有字段。 还可以通过将此参数设置为 `*` 来显式请求所有字段。

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `select`，而不是 `$select`。
> 
> 

`facet=[string]`（零或更多）- 要作为分面依据的字段。 （可选）该字符串可包含用于自定义分面的参数，以逗号分隔的 `name:value` 对表示。 有效参数为：

* `count`（最大分面词数；默认值为 10）。 没有最大值，但较高的值会产生相应的性能损失，当分面字段包含大量唯一字词时尤其如此。
  * 例如：`facet=category,count:5` 获取分面结果中的前五个类别。  
  * **注意**：如果 `count` 参数小于唯一字词数，结果可能不准确。 这由分面查询分布在各个分片上的方式所导致。 增加 `count` 通常会增加字词计数的准确性，但会损失性能。
* `sort`（`count` 之一用于按计数*降序*排序，`-count` 用于按计数*升序*排序，`value` 用于按值*升序*排序，或者 `-value` 用于按值*降序*排序）
  * 例如：`facet=category,count:3,sort:count` 按带有每个城市名称的文档数的降序在分面结果中获取前三个类别。 例如，前三个类别是 Budget、Motel 和Luxury，Budget 有 5 个命中，Motel 有 6 个，Luxury 有 4 个，然后存储桶将以 Motel、 Budget、Luxury 的顺序排序。
  * 例如，`facet=rating,sort:-value` 为所有可能的排名生成存储桶，按值降序排序。 例如，如果排名是从 1 到 5，则存储桶将排序为 5、4、3、2、1，而不考虑每个排名与多少文档匹配。
* `values`（竖线分隔数字或 `Edm.DateTimeOffset` 值，用于指定分面项值的动态组）
  * 例如：`facet=baseRate,values:10|20` 生成三个存储桶：一个用于基本费率 0 到（但不包括）10，一个用于 10 到（但不包括）20，一个用于 20 或更高。
  * 例如：`facet=lastRenovationDate,values:2010-02-01T00:00:00Z` 生成两个存储桶：一个用于在 2010 年 1 月之前翻新的酒店，一个用于在 2010 年 2 月 1 日或以后翻新的酒店。
* `interval`（对于数字，为大于 0 的整数间隔，对于日期时间值，则为 `minute`、`hour`、`day`、`week`、`month`、`quarter`、`year`）
  * 例如：`facet=baseRate,interval:100` 基于大小 100 的基本费率排名生成存储桶。 例如，如果基本费率全部在 60 美元和 600 美元之间，则有用于 0-100、100-200、200-300、300-400、400-500 和 500-600 的存储桶。
  * 例如，`facet=lastRenovationDate,interval:year` 为酒店翻新的每一年生成一个存储桶。
* `timeoffset` ([+-]hh:mm, [+-]hhmm, or [+-]hh) `timeoffset` 是可选的。 它只能与 `interval` 选项结合，并且仅在应用于 `Edm.DateTimeOffset` 类型的字段时如此。 该值指定在设置时间边界时要考虑的 UTC 时间偏移量。
  * 例如，`facet=lastRenovationDate,interval:day,timeoffset:-01:00` 使用从 01:00:00 UTC（目标时区的午夜）开始的日边界
* **注意**：`count` 和 `sort` 可以组合在同一个分面规格中，但它们无法与 `interval` 或 `values` 组合，并且 `interval` 和 `values` 无法组合在一起。
* **注意**：如果未指定 `timeoffset`，则基于 UTC 时间计算日期时间上的间隔分面。 例如：对于 `facet=lastRenovationDate,interval:day`，日边界开始于 00:00:00 UTC。 

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `facets`，而不是 `facet`。 此外，将其指定为字符串的 JSON 数组，其中每个字符串都是单独的分面表达式。
> 
> 

`$filter=[string]`（可选）- 标准 OData 语法中的结构化搜索表达式。

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `filter`，而不是 `$filter`。
> 
> 

`highlight=[string]`（可选）- 一组用于命中词突出显示的逗号分隔字段名称。 仅 `searchable` 字段可用于命中词突出显示。

`highlightPreTag=[string]`（可选，默认为 `<em>`）- 在搜索词突出显示前预置的字符串标记。 必须使用 `highlightPostTag` 设置。

> [!NOTE]
> 使用 GET 调用**搜索**时，URL 中的保留字符必须采用百分比编码（例如用 %23取代 #）。
> 
> 

`highlightPostTag=[string]`（可选，默认为 `</em>`）- 追加到命中词突出显示的字符串标记。 必须使用 `highlightPreTag` 设置。

> [!NOTE]
> 使用 GET 调用**搜索**时，URL 中的保留字符必须采用百分比编码（例如用 %23取代 #）。
> 
> 

`scoringProfile=[string]`（可选） -用于评估匹配文档的匹配分数以便为结果排序的计分概要文件的名称。

`scoringParameter=[string]`（零或更多） - 使用 `name-value1,value2,...` 格式指示计分函数（例如 `referencePointParameter`）中定义的每个参数的值。

* 例如，如果计分概要文件使用称为“mylocation”的参数定义函数，则查询字符串选项将是 `&scoringParameter=mylocation--122.2,44.8`。 第一个破折号将名称与值列表分隔开，而第二个破折号是第一个值（在此示例中为经度）的一部分。
* 对于计分参数（例如可包含逗号的标记提升），可使用单引号转义列表中的任何此类值。 如果值本身包含单引号，可通过双重单引号转义它们。
  * 例如，如果有称为“mytag”的标记提升参数并且要在值“Hello, O'Brien”和“Smith”上提升，则查询字符串选项将是 `&scoringParameter=mytag-'Hello, O''Brien',Smith`。 请注意，仅包含逗号的值需要引号。

> [!NOTE]
> 使用 POST 调用**搜索**时，此参数命名为 `scoringParameters`，而不是 `scoringParameter`。 此外，将其指定为字符串的 JSON 数组，其中每个字符串是单独的 `name-values` 对。
> 
> 

`minimumCoverage`（可选，默认为 100） - 一个介于 0 和 100 之间的数，指示为了将查询报告为成功，搜索查询必须覆盖的索引百分比。 默认情况下，整个索引都必须可用，否则 `Search` 将返回 HTTP 状态代码 503。 如果设置 `minimumCoverage` 并且 `Search` 成功，它将返回 HTTP 200 并在响应中包含 `@search.coverage` 值，指示包含在查询中的索引百分比。

> [!NOTE]
> 将此参数设置为小于 100 的值对于确保搜索可用性可能很有用，甚至对于仅有一个副本的服务也是如此。 但是，并非所有匹配的文档都保证出现在搜索结果中。 如果搜索召回率对应用程序来说比可用性更重要，则最好使 `minimumCoverage` 保留默认值 100。
> 
> 

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

注意：对于此操作，无论使用 GET 还是 POST 调用**搜索**，`api-version` 都在 URL 中指定为查询参数。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `api-key`：`api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务 URL 是唯一的。 **搜索**请求可为 `api-key` 指定管理键或查询键。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

对于 GET：无。

对于 POST：

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**部分搜索响应的延续**

有时，Azure 搜索无法在单个搜索响应中返回所有请求的结果。 发生此情况可能有不同的原因，例如当查询由于未指定 `$top` 或为 `$top` 指定了过大的值而请求过多文档时。 在这种情况下，Azure 搜索将在响应正文中包含 `@odata.nextLink` 批注，如果它是 POST 请求，还将包含 `@search.nextPageParameters`。 可使用这些批注的值表述另一个搜索请求，以获取搜索响应的下一部分。 这称为原始搜索请求的***延续***，并且这些批注通常称为***延续标记***。 有关这些批注的语法的详细信息以及它们在响应正文中显示的位置，请参阅[下面的示例](#SearchResponse)。 

Azure 搜索可能返回延续标记的原因特定于实现并且可能随时更改。 可靠的客户端应始终准备好处理返回的文档少于预期并且包含延续标记以继续检索文档的情况。 另请注意，必须使用与原始请求相同的 HTTP 方法才能继续。 例如，如果已发送 GET 请求，则发送的任何延续请求都必须也使用 GET（对于 POST 同样如此）。

<a name="SearchResponse"></a>
**响应**

对于成功的响应，返回“状态代码：200 正常”。

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**示例：**

可在 [Azure 搜索的 OData 表达式语法](https://msdn.microsoft.com/library/azure/dn798921.aspx)页面上找到其他示例。

1)    搜索按日期降序排序的索引。

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "orderby": "lastRenovationDate desc" }

2)    在分面搜索中，搜索索引并检索类别、排名、标记的分面，以及具有特定范围内的 baseRate 的项目：

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "test", "facets": [ "category", "rating", "tags", "baseRate,values:80|150|220" ] }

3)    使用筛选器，在用户单击排名 3 和类别“Motel”后缩小之前的分面查询结果的范围：

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "test", "facets": [ "tags", "baseRate,values:80|150|220" ], "filter": "rating eq 3 and category eq 'Motel'" }

4) 在分面搜索中，对查询中返回的唯一字词设置上限。 默认值为 10，但可在 `facet` 属性上使用 `count` 参数来增加或减少此值：

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview  {    "search": "test",    "facets": [ "city,count:5" ]  }

5)    搜索特定字段内的索引；例如，特定于语言的字段：

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "hôtel", "searchFields": "description_fr" }

6) 在多个字段上搜索索引。 例如，可使用多种语言存储和查询可搜索的字段，全都在同一索引内进行。  如果英语和法语说明共存于同一个文档中，则可在查询结果中返回任意或全部查询结果：

    GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview  {    "search": "hotel",    "searchFields": "description, description_fr"  }

请注意，一次只能查询一个索引。 不要为每个语言创建多个索引，除非计划一次查询一个索引。

7)    分页 - 获取项目的第 1 页（页面大小为 10）：

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "skip": 0, "top": 10 }

8)    分页 - 获取项目的第 2 页（页面大小为 10）：

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "skip": 10, "top": 10 }

9)    检索特定的字段集：

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "select": "hotelName, description" }

10)  检索与特定筛选器表达式匹配的文档：

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {  "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'" }

11) 搜索索引，并使用搜索词突出显示返回片段

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "something",   "highlight": "description" }

12) 搜索索引，并按离引用位置从近到远的顺序返回文档

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "something",   "orderby": "geo.distance(location, geography'POINT(-122.12315 47.88121)')" }

13) 搜索索引，假设有一个带有两个距离计分函数的称为“geo”的计分概要文件，一个函数定义名为“currentLocation”的参数，另一个函数定义名为“lastLocation”的参数

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&scoringParameter=lastLocation--121.499,44.2113&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "something",   "scoringProfile": "geo",   "scoringParameters": [ "currentLocation--122.123,44.77233", "lastLocation--121.499,44.2113" ] }

14) 使用[简单查询语法](https://msdn.microsoft.com/library/dn798920.aspx)在索引中查找文档。 此查询返回酒店，其中可搜索字段包含字词“舒适度”和“位置”，但不包含“汽车旅馆”：

    GET /indexes/hotels/docs?search=comfort +location -motel&searchMode=all&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "comfort +location -motel",   "searchMode": "all" }

请注意上面 `searchMode=all` 的使用。 包含此参数将重写 `searchMode=any` 的默认值，确保 `-motel` 表示“AND NOT”，而不是“OR NOT”。 如果没有 `searchMode=all`，将获取“OR NOT”，从而展开而不是限制搜索结果，这对某些用户来说可能违反语感。

15) 使用 [lucene 查询语法](https://msdn.microsoft.com/library/mt589323.aspx)在索引中查找文档。 此查询返回酒店，其中类别字段包含字词“budget”和所有包含短语“recently renovated”的可搜索字段。 包含短语“recently renovated”的文档作为字词提升值 (3) 的结果排名更高

    GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28-Preview&querytype=full

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "category:budget AND \"recently renovated\"^3",   "queryType": "full",   "searchMode": "all" }

<a name="LookupAPI"></a>

## <a name="lookup-document"></a>查找文档
**查找文档**操作从 Azure 搜索中检索文档。 当用户单击特定搜索结果并且你希望查找关于该文档的特定详细信息时，这很有用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**请求**

对于服务请求，需要 HTTPS。 **查找文档**请求可按以下方式构造。

    GET /indexes/[index name]/docs/key?[query parameters]

此外，也可以为键查找使用传统 OData 语法：

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

请求 URI 包含 [index name] 和 [key]，指定要从哪个索引检索哪个文档。 一次只能获取一个文档。 使用**搜索**在单个请求中获取多个文档。

**查询参数**

`$select=[string]`（可选）- 要检索的字段的逗号分隔列表。 如果未指定或设置为 `*`，将在投影中包含架构中标记为可检索的所有字段。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

注意：对于此操作，`api-version` 指定为查询参数。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `api-key`：`api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务 URL 是唯一的。 **查找文档**请求可为 `api-key` 指定管理键或查询键。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

无。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**示例**

查找具有键“2”的文档

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

使用 OData 语法查找具有键“3”的文档：

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>

## <a name="count-documents"></a>计数文档
**计数文档**操作在搜索索引中检索文档数的计数。 `$count` 语法是 OData 协议的一部分。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**请求**

对于服务请求，需要 HTTPS。 **计数文档**请求可使用 GET 方法构造。

请求 URI 中的 [index name] 告诉服务返回指定索引的文档集合中所有项目的计数。

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

**请求标头**

以下列表介绍必需和可选的请求标头。

* `Accept`：此值必须设置为 `text/plain`。
* `api-key`：`api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务 URL 是唯一的。 **计数文档**请求可为 `api-key` 指定管理键或查询键。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

无。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

响应正文以纯文本格式的整数形式包含计数值。

<a name="Suggestions"></a>

## <a name="suggestions"></a>建议
**建议**操作基于部分搜索输入检索建议。 它通常在搜索框中使用，用于在用户正在输入搜索词时提供提前键入建议。

建议请求旨在建议目标文档，因此如果有多个候选文档与同一个搜索输入匹配，建议的文本可以重复。 可使用 `$select` 检索其他文档字段（包括文档键），以便你可以分辨哪个文档是每个建议的源。

**建议**操作作为 GET 或 POST 请求发出。

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**何时使用 POST 而不是 GET**

使用 HTTP GET 调用**建议** API 时，需要了解请求 URL 的长度不能超过 8 KB。 对于大多数应用程序，这通常已足够。 但是，某些应用程序产生非常大的查询，尤其是 OData 筛选器表达式。 对于这些应用程序，使用 HTTP POST 是更好的选择，因为它允许比 GET 更大的筛选器。 对于 POST，限制因素是筛选器中子句的数量，而不是原始筛选器字符串的大小，因为 POST 的请求大小限制大约为 16 MB。

> [!NOTE]
> 即使 POST 请求大小限制非常大，筛选器表达式也不能任意复杂。 有关筛选器复杂性限制的详细信息，请参阅 [OData 表达式语法](https://msdn.microsoft.com/library/dn798921.aspx)。
> 
> 

**请求**

对于服务请求，需要 HTTPS。 可使用 GET 或 POST 方法构造**建议**请求。

请求 URI 指定要查询的索引的名称。 对于 GET 请求，参数（如部分输入的搜索词）在查询字符串上指定，对于 POST 请求，参数在请求正文中指定。

作为创建 GET 请求时的最佳做法，请记得在直接调用 REST API 时对特定查询参数进行 [URL 编码](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx)。 对于**建议**请求，这包括：

* `$filter`
* `highlightPreTag`
* `highlightPostTag`
* `search`

仅对上述查询参数建议使用 URL 编码。 如果无意中对整个查询字符串（? 后的所有内容）进行 URL 编码，请求将中断。

此外，仅在使用 GET 直接调用 REST API 时，必须进行 URL 编码。 使用 POST 调用**建议**或使用 [.NET 客户端库](https://msdn.microsoft.com/library/dn951165.aspx)（为你处理 URL 编码）时，不需要 URL 编码。

**查询参数**

**建议**接受多个参数，这些参数提供查询标准，还指定搜索行为。 当通过 GET 调用**建议**时，在 URL 查询字符串中提供这些属性，当通过 POST 调用**建议**时，提供这些参数作为请求正文中的 JSON 属性。 在 GET 和 POST 之间，某些参数的语法稍有不同。 这些差异在下面注明为适用：

`search=[string]` - 要用于建议查询的搜索文本。 必须至少为 1 个字符，并且不超过 100 个字符。

`highlightPreTag=[string]`（可选）- 在搜索词前预置的字符串标记。 必须使用 `highlightPostTag` 设置。

> [!NOTE]
> 使用 GET 调用**建议**时，URL 中的保留字符必须采用百分比编码（例如用 %23取代 #）。
> 
> 

`highlightPostTag=[string]`（可选）- 附加到搜索词的字符串标记。 必须使用 `highlightPreTag` 设置。

> [!NOTE]
> 使用 GET 调用**建议**时，URL 中的保留字符必须采用百分比编码（例如用 %23取代 #）。
> 
> 

`suggesterName=[string]` - 在作为索引定义一部分的 `suggesters` 集合中指定的建议器名称。 `suggester` 确定在哪些字段中扫描建议的查询词。 有关详细信息，请参阅[建议器](#Suggesters)。

`fuzzy=[boolean]`（可选，默认值 = false）- 当设置为 true 时，此 API 将查找建议，即使搜索文本中存在替代或缺少的字符也是如此。 尽管这在某些方案中提供更好的体验，但它以性能为代价，因为模糊建议搜索更慢，并且消耗更多资源。

`searchFields=[string]`（可选）- 要搜索指定搜索文本的逗号分隔字段名称的列表。 必须为建议启用目标字段。

`$top=#`（可选，默认值 = 5）- 要检索的建议数。 必须是介于 1 和 100 之间的数。

> [!NOTE]
> 使用 POST 调用**建议**时，此参数命名为 `top`，而不是 `$top`。
> 
> 

`$filter=[string]`（可选）- 筛选为建议所考虑的文档的表达式。

> [!NOTE]
> 使用 POST 调用**建议**时，此参数命名为 `filter`，而不是 `$filter`。
> 
> 

`$orderby=[string]`（可选）- 为结果排序所依据的逗号分隔表达式的列表。 每个表达式可以是字段名称或对 `geo.distance()` 函数的调用。 每个表达式可以后跟 `asc` 来指示升序，后跟 `desc` 来指示降序。 默认值为升序。 对于 `$orderby`，有 32 个子句的限制。

> [!NOTE]
> 使用 POST 调用**建议**时，此参数命名为 `orderby`，而不是 `$orderby`。
> 
> 

`$select=[string]`（可选）- 要检索的字段的逗号分隔列表。 如果未指定，则仅返回文档键和建议文本。 可通过将此参数设置为 `*` 显式请求所有字段。

> [!NOTE]
> 使用 POST 调用**建议**时，此参数命名为 `select`，而不是 `$select`。
> 
> 

`minimumCoverage`（可选，默认为 80） - 一个介于 0 和 100 之间的数，指示为了将查询报告为成功，建议查询必须覆盖的索引百分比。 默认情况下，至少 80% 的索引必须可用，否则 `Suggest` 将返回 HTTP 状态代码 503。 如果设置 `minimumCoverage` 并且 `Suggest` 成功，它将返回 HTTP 200 并在响应中包含 `@search.coverage` 值，指示包含在查询中的索引百分比。

> [!NOTE]
> 将此参数设置为小于 100 的值对于确保搜索可用性可能很有用，甚至对于仅有一个副本的服务也是如此。 但是，并非所有匹配的建议都保证出现在结果中。 如果召回率对应用程序来说比可用性更重要，则最好不要将 `minimumCoverage` 降到其默认值 80 以下。
> 
> 

`api-version=[string]`（必需）。 预览版为 `api-version=2015-02-28-Preview`。 有关详细信息和备用版本，请参阅[搜索服务版本控制](http://msdn.microsoft.com/library/azure/dn864560.aspx)。

注意：对于此操作，无论使用 GET 还是 POST 调用**建议**，`api-version` 都在 URL 中指定为查询参数。

**请求标头**

以下列表介绍必需和可选的请求标头

* `api-key`：`api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务 URL 是唯一的。 **建议**请求可将管理键或查询键指定为 `api-key`。

还需要服务名称才能构造请求 URL。 可从 Azure 门户中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建 Azure 搜索服务](search-create-service-portal.md)。

**请求正文**

对于 GET：无。

对于 POST：

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**响应**

对于成功的响应，返回“状态代码：200 正常”。

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

如果投影选项用于检索字段，则它们包含在数组的每个元素中：

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**示例**

检索 5 个建议，其中部分搜索输入是“lux”

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }

