---
title: 升级 REST API 版本
titleSuffix: Azure Cognitive Search
description: 查看 API 版本的差异，了解将现有代码迁移到最新 Azure 认知搜索服务 REST API 版本所需的操作。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929636"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>升级到 Azure 认知搜索服务中最新的 REST API

如果你使用的是[搜索 REST API](/rest/api/searchservice/) 的早期版本，本文将有助于将应用程序升级到正式发布的最新 API 版本 2020-06-30 。

版本 2020-06-30 包含一项重要的新功能（[知识存储](knowledge-store-concept-intro.md)），并引入了几项小的行为变更。 因此，此版本主要向后兼容，从而如果从前一版 (2019-05-06) 升级，代码更改应该极少。

> [!NOTE]
> 搜索服务支持多种 REST API 版本，包括早期版本。 你可以继续使用这些 API 版本，但我们建议将代码迁移到最新版本，以便可以访问新功能。 随着时间的推移，最陈旧的 REST API 版本将会被弃用，并且[不会再受支持](search-api-versions.md#unsupported-versions)。

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>如何升级

在升级到新版本时，除了更改版本号之外，可能不会需要对代码进行很多其他更改。 仅对于以下情况，可能需要更改代码：

* 当 API 响应中返回无法识别的属性时，代码失效。 默认情况下，应用程序应忽略无法理解的属性。

* 代码仍坚持 API 请求，并尝试将其重新发送到新 API 版本。 例如，如果应用程序仍存留从搜索 API 返回的延续标记（有关详细信息，请查找[搜索 API 参考](/rest/api/searchservice/Search-Documents)中的 `@search.nextPageParameters`）。

* 你的代码引用的 API 版本早于 2019-05-06，并且受该版本中一项或多项中断性变更的影响。 [升级到 2019-05-06](#upgrade-to-2019-05-06) 部分提供了更多详细信息。 

如果其中任一种情况适用，则可能需要相应地更改代码。 否则，尽管你可能需要开始使用新版本中添加的功能，也无需进行任何更改。

## <a name="upgrade-to-2020-06-30"></a>升级到 2020-06-30

版本 2020-06-30 是 REST API 的新正式版。 存在一个重大更改和几个行为差异。 

此 API 版本中现在通常可用的功能包括：

* [知识存储](knowledge-store-concept-intro.md)，通过技能组创建的扩充内容的持久存储，创建的目的是通过其他应用程序进行下游分析和处理。 有了此功能，索引器驱动的 AI 扩充管道除了可以填充搜索索引外，还可以填充知识存储。 如果你使用了此功能的预览版，则它相当于正式版。 唯一需要的代码更改是修改 api-version。

### <a name="breaking-change"></a>重大更改

如果代码包含以下功能，则针对早期 API 版本编写的现有代码将在 API 版本 = 2020-06-30 和更高版本上中断：

* 任何 Edm。日期文字 (由月份日期组成的日期，例如 `2020-12-12` 筛选器表达式中的) 必须遵循 Edm. DateTimeOffset 格式： `2020-12-12T00:00:00Z` 。 需要进行此更改才能处理错误或意外的查询结果，因为存在时区差异。

### <a name="behavior-changes"></a>行为更改

* [BM25 排名算法](index-ranking-similarity.md)将以前的排名算法替换为更新的技术。 新服务将自动使用此算法。 对于现有服务，必须将参数设置为使用新算法。

* 在此版本中，NULL 值的排序结果发生了变化，如果排序是 `asc`，则 NULL 值首先出现；如果排序是 `desc`，则 NULL 值最后出现。 如果你编写代码来处理 NULL 值的排序方式，请注意此更改。

## <a name="upgrade-to-2019-05-06"></a>升级到 2019-05-06

版本 2019-05-06 是 REST API 的上一个正式版。 此 API 版本中正式可用的功能包括：

* [自动完成](index-add-suggesters.md)是一项自动提示功能，可以完成部分指定的字词输入。
* [复杂类型](search-howto-complex-data-types.md)原生支持搜索索引中的结构化对象数据。
* [JsonLines 分析模式](search-howto-index-json-blobs.md)（Azure Blob 编制索引的一部分）可为每个 JSON 实体创建以换行符分隔的搜索文档。
* [AI 扩充](cognitive-search-concept-intro.md)提供可以利用认知服务 AI 扩充引擎的索引编制功能。

### <a name="breaking-changes"></a>重大变化

如果代码包含以下功能，针对早期 API 版本编写的现有代码将在遇到 api-version = 2019-05-06 及更高版本时中断运行：

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB - 数据源的索引器现在是 "type": "cosmosdb"

如果使用 [Cosmos DB 索引器](search-howto-index-cosmosdb.md )，必须将 `"type": "documentdb"` 更改为 `"type": "cosmosdb"`。

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>索引器执行结果错误不再提供状态

索引器执行的错误结构以前包含 `status` 元素。 此元素已被删除，因为它不能提供有用的信息。

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>索引器数据源 API 不再返回连接字符串

从 API 版本 2019-05-06 和 2019-05-06-Preview 开始，数据源 API 不再在任何 REST 操作的响应中返回连接字符串。 在以前的 API 版本中，对于使用 POST 创建的数据源，Azure 认知搜索会返回 **201** 后接 OData 响应，该响应包含纯文本格式的连接字符串。

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>“命名实体识别”认知技能现已停用

如果在代码中调用[命名实体识别](cognitive-search-skill-named-entity-recognition.md)技能，调用会失败。 替代的功能是[实体识别](cognitive-search-skill-entity-recognition.md)。 无需进行任何其他更改应该就能替换技能引用。 两个版本的 API 签名相同。 

### <a name="upgrading-complex-types"></a>升级复杂类型

API 版本 2019-05-06 添加了对复杂类型的正式支持。 如果你的代码在 2017-11-11-Preview 或 2016-09-01-Preview 中实现了以前关于复杂类型等效性的建议，那么从 2019-05-06 版本开始，你需要注意一些新的和更改的限制：

+ 每个索引的子字段深度和复杂集合数目限制已降低。 如果使用预览版 API 创建了超出这些限制的索引，尝试使用 API 版本 2019-05-06 更新或重新创建这些索引将会失败。 如果遇到这种情况，需要重新设计架构，使其不会超出新的限制，然后重新生成索引。

+ 从 API 版本 2019-05-06 开始，对每个文档的复杂集合元素数目施加了新的限制。 如果使用预览版 API 创建了文档超出这些限制的索引，尝试使用 API 版本 2019-05-06 重建该数据的索引将会失败。 如果遇到这种情况，需要在重建数据的索引之前，减少每个文档的复杂集合元素数目。

有关详细信息，请参阅 [Azure 认知搜索的服务限制](search-limits-quotas-capacity.md)。

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>如何升级旧的复杂类型结构

如果代码使用在早期 API 预览版中创建的复杂类型，则你可能正在使用如下所示的索引定义格式：

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

API 版本 2017-11-11-Preview 中引入了一种更新的用于定义索引字段的树形格式。 在新格式中，每个复杂字段提供一个字段集合，可在该集合中定义该字段的子字段。 在 API 版本 2019-05-06 中，此新格式是以独占方式使用的，使用旧格式尝试创建或更新索引将会失败。 如果索引是使用旧格式创建的，则你需要使用 API 版本 2017-11-11-Preview 将其更新到新格式，然后才能使用 API 版本 2019-05-06 来管理它们。

可以执行以下步骤使用 API 版本 2017-11-11-Preview 将“平面”索引更新为新格式：

1. 执行 GET 请求以检索索引。 如果该索引已采用新格式，则无需执行后续操作。

2. 将索引从“平面”格式转换为新格式。 必须为此操作编写代码，因为在编写本文时尚未有示例代码可用。

3. 执行 PUT 请求，将索引更新为新格式。 切勿更改索引的任何其他详细信息（例如字段的可搜索性/可筛选性），因为更新索引 API 不允许这样做。

> [!NOTE]
> 无法在 Azure 门户中管理使用旧的“平面”格式创建的索引。 请尽早将索引的“平面”表示形式升级到“树形”表示形式。

## <a name="next-steps"></a>后续步骤

查看搜索 REST API 参考文档。 如果遇到问题，请向我们提供有关 [Stack Overflow](https://stackoverflow.com/) 的帮助或 [联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 参考](/rest/api/searchservice/)
