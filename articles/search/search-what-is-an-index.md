---
title: 创建搜索索引
titleSuffix: Azure Cognitive Search
description: 介绍 Azure 认知搜索中的索引概念和工具，包括架构定义和物理数据结构。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3aa4a1917711f8997c282ba577c33e7a7f94472b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932876"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>在 Azure 认知搜索中创建基本搜索索引

在 Azure 认知搜索中，搜索索引会存储用于全文查询和筛选后查询的可搜索内容。 索引由架构定义并保存到服务，紧接着的第二步是数据导入。 

索引包含文档。 从概念上讲，文档是索引中的一个可搜索数据单元。 零售商可能有每件产品的文档，新闻机构可能有每篇报道的文档。 将这些概念对应到更为熟悉的数据库等效对象：搜索索引等同于表，文档大致相当于表中的行   。

索引的物理结构由架构确定，标记为“可搜索”的字段会得到一个为其创建的倒排索引。 

可以使用以下工具和 API 创建索引：

* 在 Azure 门户中，使用“添加索引”或“导入数据”向导 
* 使用[创建索引 (REST API)](/rest/api/searchservice/create-index)
* 使用 [.NET SDK](./search-get-started-dotnet.md)

使用门户工具可方便学习。 门户强制实施针对特定数据类型的要求和架构规则，例如，对数值字段禁用全文搜索功能。 获得可使用的索引后，可以通过使用[获取索引 (REST API)](/rest/api/searchservice/get-index) 从服务检索 JSON 定义并将其添加到解决方案，来转换到代码。

## <a name="recommended-workflow"></a>建议的工作流

形成最终索引设计的过程是一个不断更迭的过程。 常见的方法是，先从门户创建初始索引，然后切换到代码，将索引置于源代码管理下。

1. 确定是否可以使用[“导入数据”](search-import-data-portal.md)。 如果源数据来自 [Azure 中受支持的数据源类型](search-indexer-overview.md#supported-data-sources)，向导会编制基于索引器的一体式索引。

1. 如果无法使用“导入数据”，可以先使用“添加索引”来定义架构 。

   ![添加索引命令](media/search-what-is-an-index/add-index.png "添加索引命令")

1. 提供用于对索引中每个搜索文档进行唯一标识的名称和键。 键是必需项并且必须为 Edm.String 类型。 在导入过程中，应规划将源数据中的唯一字段映射到此字段。 

   门户会提供键的 `id` 字段。 若要替代默认 `id`，请创建新字段（例如，名为 `HotelId` 的新字段定义），然后在“键”中选择它。

   ![填写所需属性](media/search-what-is-an-index//field-attributes.png "填写所需属性")

1. 添加更多字段。 门户会显示不同数据类型可用的[字段属性](#index-attributes)。 如果你不太熟悉索引设计，此功能非常有用。

   如果传入数据在本质上是分层的，则分配[复杂类型](search-howto-complex-data-types.md)数据类型来表示嵌套结构。 内置的示例数据集 Hotels 演示的复杂类型使用一个 Address（包含多个子字段），其中有与每个酒店的一对一关系，并且有一个 Rooms 复杂集合，其中有多个房间与每个酒店相关联。 

1. 在创建索引之前，将所有[分析器](#analyzers)分配给字符串字段。 如果要在特定字段上启用自动完成功能，请为[建议器](#suggesters)执行相同的操作。

1. 单击“创建”在搜索服务中生成物理结构。

1. 创建索引后，使用其他命令查看定义或添加更多元素。

   ![“添加索引”页，其中按数据类型显示了属性](media/search-what-is-an-index//field-definitions.png "“添加索引”页，其中按数据类型显示了属性")

1. 使用[获取索引 (REST API)](/rest/api/searchservice/get-index) 和 [Postman](search-get-started-postman.md) 等 Web 测试工具下载索引架构。 现在你有可以为代码进行调整的索引的 JSON 表示形式。

1. [加载索引和数据](search-what-is-data-import.md)。 Azure 认知搜索接受 JSON 文档。 若要以编程方式加载数据，可以在请求有效负载中使用包含 JSON 文档的 Postman。 如果无法轻松将数据表示为 JSON，此步骤耗费的精力是最大的。 

    将索引随数据一并加载后，对现有字段执行的大多数编辑操作都需要删除和重新生成索引。

1. 查询索引，检查结果，并进一步迭代索引架构，直到开始看到预期的结果。 可以使用[**搜索资源管理器**](search-explorer.md)或 Postman 来查询索引。

在开发过程中，规划频繁的重新生成。 由于物理结构是在服务中创建的，对现有的字段定义进行的许多更改，都必须[删除并重新创建索引](search-howto-reindex.md)。 可以考虑使用一部分数据来加快重新生成的速度。 

> [!Tip]
> 建议在索引设计和数据导入中都使用代码（而不是门户方法）。 如果开发项目仍处于早期阶段，[Postman 和 REST API](search-get-started-postman.md) 等备选工具也有助于完成概念证明测试。 可对请求正文中的索引定义进行增量更改，然后将请求发送到服务，以使用更新的架构重新创建索引。

## <a name="index-schema"></a>索引架构

索引需要在字段集合中有一个名称和一个指定的键字段（为 Edm.string）。 [*字段集合*](#fields-collection)通常是索引的最大组成部分，其中每个字段都已命名、类型化，并具有允许行为的属性（确定该字段的用法）。 

其他元素包括[建议器](#suggesters)、[计分概要文件](#scoringprofiles)、[分析器](#analyzers)，分析器用于根据语言规则或分析器支持的其他特性将字符串处理成令牌，以及[跨域远程脚本 (CORS)](#corsoptions) 设置。

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
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>字段集合与字段属性

字段具有名称、用于对存储数据进行分类的类型，以及用于指定如何使用字段的属性。

### <a name="data-types"></a>数据类型

| 类型 | 说明 |
|------|-------------|
| Edm.String |全文搜索可以选择性地标记化（断词、词干提取等）的文本。 |
| 集合 (Edm.String) |全文搜索可以选择性标记化的字符串列表。 理论上，集合中的项目数没有上限，但集合的有效负载大小上限为 16 MB。 |
| Edm.Boolean |包含 true/false 值。 |
| Edm.Int32 |32 位整数值。 |
| Edm.Int64 |64 位整数值。 |
| Edm.Double |双精度数字数据。 |
| Edm.DateTimeOffset |以 OData V4 格式表示的日期时间值（例如 `yyyy-MM-ddTHH:mm:ss.fffZ` 或 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`）。 |
| Edm.GeographyPoint |表示地球上的地理位置的点。 |

有关详细信息，请参阅[支持的数据类型](/rest/api/searchservice/Supported-data-types)。

<a name="index-attributes"></a>

### <a name="attributes"></a>特性

字段属性决定了字段的使用方式，例如，是否用于全文搜索、分面导航和排序等操作中。 

字符串字段通常标记为“searchable”和“retrievable”。 用来缩小搜索结果范围的字段包括“sortable”、“filterable”和“facetable”。

|Attribute|描述|  
|---------------|-----------------|  
|“searchable” |可全文搜索，在编制索引期间遵从语法分析，例如分词。 如果将某个可搜索字段设置为“sunny day”之类的值，在内部它将拆分为单独的标记“sunny”和“day”。 有关详细信息，请参阅[全文搜索工作原理](search-lucene-query-architecture.md)。|  
|“filterable” |在 $filter 查询中引用。 `Edm.String` 或 `Collection(Edm.String)` 类型的可筛选字段不进行分词，因此，比较仅用于查找完全匹配项。 例如，如果将此类字段 f 设置为“sunny day”，则 `$filter=f eq 'sunny'` 将找不到任何匹配项，但 `$filter=f eq 'sunny day'` 可找到。 |  
|“sortable” |默认情况下，系统按分数对结果进行排序，但可以配置基于文档中字段的排序。 `Collection(Edm.String)` 类型的字段不能为“sortable”。 |  
|“facetable” |通常用于包括了按类别（例如特定城市中的宾馆）的命中次数的搜索结果呈现中。 此选项无法与 `Edm.GeographyPoint` 类型的字段一起使用。 `Edm.String` 类型的字段为可筛选，“sortable”或“facetable”字段的长度最多可以是 32 千字节。 有关详细信息，请参阅[创建索引 (REST API)](/rest/api/searchservice/create-index)。|  
|“key” |文档在索引内的唯一标识符。 必须仅选择单个字段作为键字段，并且它必须是 `Edm.String` 类型的。|  
|“retrievable” |决定了是否可以在搜索结果中返回此字段。 当希望将某个字段（例如“利润”）用作筛选器、排序或评分机制，但不希望该字段显示给最终用户时，这很有用。** 对于 `key` 字段，此属性必须为 `true`。|  

尽管可以随时添加新字段，但在索引的生存期内现有字段定义将被锁定。 因此，开发人员通常使用门户创建简单索引、测试创意，或者使用门户页面来查找设置。 如果采用基于代码的方式以便可以轻松重新生成索引，那么对索引进行频繁迭代的设计就更为高效。

> [!NOTE]
> 用于生成索引的 API 具有不同的默认行为。 对于 [REST API](/rest/api/searchservice/Create-Index)，大多数属性在默认情况下处于启用状态（例如，“searchable”和“retrievable”对于字符串字段为 true），并且通常只需要在要关闭它们时设置它们。 对于 .NET SDK，情况恰恰相反。 对于未显式设置的任何属性，默认情况下禁用相应的搜索行为，除非你特别启用它。

## `analyzers`

分析器元素设置用于字段的语言分析器的名称。 有关可用的分析器范围的详细信息，请参阅[将分析器添加到 Azure 认知搜索索引](search-analyzers.md)。 分析器仅适用于可搜索字段。 将分析器分配到字段后，除非重新生成索引，否则无法更改分析器。

## `suggesters`

建议器是定义要使用索引中的哪些字段来支持搜索中的自动填写或提前键入查询的架构部分。 通常，当用户尝试键入搜索查询时，部分搜索字符串将发送到[建议 (REST API)](/rest/api/searchservice/suggestions)，并且该 API 会返回一组建议的文档或短语。 

添加到建议器的字段用于生成自动提示搜索词。 在索引编制期间创建所有搜索词，并单独存储它们。 有关创建建议器结构的详细信息，请参阅[添加建议器](index-add-suggesters.md)。

## `corsOptions`

默认情况下，客户端 JavaScript 无法调用任何 API，因为浏览器将阻止所有跨域请求。 若要允许对索引进行跨域查询，请通过设置 **corsOptions** 来启用 CORS（跨域资源共享）。 出于安全原因，只有查询 API 才支持 CORS。 

可为 CORS 设置以下选项：

+ **allowedOrigins**（必需）：这是会被授予索引访问权限的来源的列表。 这意味着，将允许从这些来源提供的任何 JavaScript 代码查询索引（假设它提供正确的 api-key）。 每个来源通常采用 `protocol://<fully-qualified-domain-name>:<port>` 格式，不过往往会省略 `<port>`。 有关更多详细信息，请参阅 [跨域资源共享 (维基百科) ](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 。

  若要允许访问所有来源，请将 `*` 作为单个项目包含在 **allowedOrigins** 数组中。 不建议对生产搜索服务采用这种做法，但它在开发和调试中却很有用。 

+ **maxAgeInSeconds**（可选）：浏览器使用此值确定缓存 CORS 预检响应的持续时间（以秒为单位）。 此值必须是非负整数。 此值越大，性能越好，但 CORS 策略更改生效所需的时间也越长。 如果未设置此值，将使用 5 分钟的默认持续时间。

## `scoringProfiles`

[评分配置文件](index-add-scoring-profiles.md)是定义自定义评分行为，方便用户影响搜索结果中排名更高的项的架构部分。 计分配置文件由字段权重和函数组成。 若要使用它们，请在查询字符串上按名称指定配置文件。

在幕后运行的默认计分概要文件，用于为结果集中的每个项目计算搜索分数。 可使用内部、未命名的计分概要文件。 或者，将 **defaultScoringProfile** 设置为使用自定义配置文件作为默认配置文件，每当未在查询字符串上指定自定义配置文件时，将调用该配置文件。

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>属性和索引大小（存储影响）

索引大小由上传的文档的大小以及索引配置（例如，是否包括建议器，以及如何在各个字段上设置属性）决定。 

以下屏幕截图演示了各种属性组合产生的索引存储模式。 索引基于“房地产示例索引”，你可以使用“导入数据”向导轻松创建此索引。 尽管未显示索引架构，但可以基于索引名称推断属性。 例如，只选择了 realestate-searchable 索引中的“searchable”属性，只选择了 realestate-retrievable 索引中的“retrievable”属性，等等 。

![基于属性选择的索引大小](./media/search-what-is-an-index/realestate-index-size.png "基于属性选择的索引大小")

尽管这些索引变体是人造的，但我们可以参考这些变体来对属性影响存储的方式进行广泛比较。 设置“retrievable”是否会增大索引大小？ 否。 将字段添加到 suggester 是否会增大索引大小？ 可以。

支持筛选和排序的索引在比例上大于仅支持全文搜索的索引。 这是因为筛选和排序操作扫描完全匹配项，并要求存在逐字文本字符串。 相比之下，支持全文查询的可搜索字段使用倒排索引，而这些索引中填充了空间占用量比整个文档更小的标记化字词。 

> [!Note]
> 存储体系结构被视为 Azure 认知搜索的实现细节，随时可能在不另行通知的情况下进行更改。 不保证将来仍会保持当前的行为。

## <a name="next-steps"></a>后续步骤

了解索引的构成后，可以继续在门户中创建第一个索引。 建议从“导入数据”向导开始，选择承载 realestate-us-sample 或 hotels-sample 的数据源 。

> [!div class="nextstepaction"]
> [导入数据向导（门户）](search-get-started-portal.md)

对于这两个数据集，向导可以推断索引架构，导入数据，并输出可使用搜索资源管理器查询的可搜索索引。 在“导入数据”向导的“连接到数据”页中查找这些数据源 。

   ![创建示例索引](media/search-what-is-an-index//import-wizard-sample-data.png "创建示例索引")