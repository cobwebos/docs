---
title: 升级到最新的 Azure 认知搜索服务 REST API 版本
titleSuffix: Azure Cognitive Search
description: 查看 API 版本中的差异并了解将现有代码迁移到最新的 Azure 认知搜索服务 REST API 版本所需的操作。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9bffb41cce030b7a63e600e5ffaf65130261b4c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791157"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>升级到最新的 Azure 认知搜索服务 REST API 版本

如果你使用的是早期版本的[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)，本文将帮助你升级应用程序以使用最新的已正式发布的 API 版本2019-05-06。

版本2019-05-06 的 REST API 包含早期版本中的一些更改。 这些更改主要涉及向后兼容性，因此更改代码只需最小的工作量，具体取决于之前使用的是哪个版本。 [升级步骤](#UpgradeSteps)概述了使用新功能所需的代码更改。

> [!NOTE]
> Azure 认知搜索服务实例支持一系列 REST API 版本，包括早期版本。 您可以继续使用这些 API 版本，但我们建议将您的代码迁移到最新版本，以便您可以访问新功能。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>版本2019-05-06 中的新增功能
版本2019-05-06 是 REST API 的最新公开发布版本。 在此 API 版本中已转换为正式可用状态的功能包括：

* ["自动完成"](index-add-suggesters.md)是一种 typeahead 功能，用于完成部分指定的术语输入。

* [复杂类型](search-howto-complex-data-types.md)为搜索索引中的结构化对象数据提供本机支持。

* [JsonLines 分析模式](search-howto-index-json-blobs.md)是 Azure Blob 索引的一部分，它为每个由换行符分隔的 JSON 实体创建一个搜索文档。

* [Ai 扩充](cognitive-search-concept-intro.md)提供的索引利用了认知服务的 AI 扩充引擎。

几个预览功能版本与此正式发布的更新一致。 若要查看新预览功能的列表，请参阅[搜索 REST api-版本 2019-05-06-preview](search-api-preview.md)。

## <a name="breaking-changes"></a>重大变化

包含以下功能的现有代码将在 api 版本 = 2019-05-06 中断。

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB 数据源的索引器现在为 "type"： "cosmosdb"

如果使用的是[Cosmos DB 索引器](search-howto-index-cosmosdb.md )，则必须将 `"type": "documentdb"` 改为 `"type": "cosmosdb"`。

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>索引器执行结果错误不再具有状态

索引器执行的错误结构之前有一个 `status` 元素。 已删除此元素，因为它没有提供有用的信息。

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>索引器数据源 API 不再返回连接字符串

从 API 版本2019-05-06 和 2019-05-06-向前预览，数据源 API 不再在任何 REST 操作响应中返回连接字符串。 在以前的 API 版本中，对于使用 POST 创建的数据源，Azure 认知搜索返回**201** ，后跟 OData 响应，其中包含纯文本形式的连接字符串。

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>已命名实体识别认知技能现已停止

如果在代码中调用[名称实体识别](cognitive-search-skill-named-entity-recognition.md)技能，则调用将失败。 替换功能是[实体识别](cognitive-search-skill-entity-recognition.md)。 您应该能够替换技能参考，而不需要进行其他更改。 这两个版本的 API 签名相同。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤
如果要从以前的 GA 版本2017-11-11 或2016-09-01 升级，则不需要对代码进行任何更改，而只需更改版本号。 仅对于以下情况，可能需要更改代码：

* 当 API 响应中返回无法识别的属性时，代码失效。 默认情况下，应用程序应忽略无法理解的属性。

* 代码仍坚持 API 请求，并尝试将其重新发送到新 API 版本。 例如，如果应用程序仍存留从搜索 API 返回的延续标记（有关详细信息，请查找[搜索 API 参考](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)中的 `@search.nextPageParameters`）。

如果任一种情况适用，都可能需要更改相应代码。 否则，除非要开始使用版本2019-05-06 的[新功能](#WhatsNew)，否则不需要进行任何更改。

如果要从预览版 API 升级，以上内容也适用，但你还必须注意，版本2019-05-06 中不提供某些预览功能：

* ["更喜欢此" 查询](search-more-like-this.md)
* [CSV blob 索引](search-howto-index-csv-blobs.md)
* [Cosmos DB 索引器的 MongoDB API 支持](search-howto-index-cosmosdb.md)

如果你的代码使用这些功能，则将无法升级到 API 2019-05-06 版，而不会删除你的使用。

> [!IMPORTANT]
> 预览 API 用于测试和评估，不应在生产环境中使用。
> 

### <a name="upgrading-complex-types"></a>升级复杂类型

如果你的代码使用复杂类型，并且使用的是较旧的预览 API 版本 2017-11-11-预览版或 2016-09-01-Preview，则需要注意版本2019-05-06 的一些新的和更改的限制：

+ 每个索引的子字段深度和复杂集合数的限制已降低。 如果使用预览 api 版本创建了超出这些限制的索引，则任何使用 API 版本2019-05-06 更新或重新创建它们的尝试都将失败。 如果这适用于你，你将需要重新设计架构以适应新的限制，然后重新生成索引。

+ Api 版本2019-05-06 中存在新的限制，即每个文档的复杂集合的元素数。 如果使用预览 api 版本创建了包含超过这些限制的文档的索引，则任何使用 api 版本2019-05-06 重新索引该数据的尝试都将失败。 如果这适用于你，则需要减少对数据重新编制索引之前每个文档的复杂集合元素数。

有关详细信息，请参阅[Azure 认知搜索的服务限制](search-limits-quotas-capacity.md)。

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>如何升级旧的复杂类型结构

如果你的代码将复杂类型与较旧的预览 API 版本之一一起使用，则可能会使用如下所示的索引定义格式：

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

API 版本 2017-11-11-Preview 中引入了新的与树类似的定义索引字段格式。 在新格式中，每个复杂字段都有一个字段集合，其中定义了其子字段。 在 API 版本2019-05-06 中，此新格式专门使用，尝试使用旧格式创建或更新索引将失败。 如果索引是使用旧格式创建的，则需要使用 API 版本 2017-11-11-Preview 将其更新为新的格式，然后才能使用 API 版本2019-05-06 进行管理。

可以通过以下步骤使用 API 版本 2017-11-11-Preview 将 "平面" 索引更新为新格式：

1. 执行 GET 请求以检索索引。 如果已是新格式，则已完成。

2. 将索引从 "平面" 格式转换为新格式。 由于编写本文时没有可用的示例代码，因此您必须为此编写代码。

3. 执行 PUT 请求，将索引更新为新格式。 请确保不要更改索引的任何其他详细信息，如字段的可搜索性/filterability，因为更新索引 API 不允许这样做。

> [!NOTE]
> 不能从 Azure 门户管理用旧 "平面" 格式创建的索引。 请尽早将索引从 "平面" 表示形式升级为 "树" 表示形式。

## <a name="next-steps"></a>后续步骤

查看搜索 REST API 参考文档。 如果遇到问题，请向我们提供有关[StackOverflow](https://stackoverflow.com/)的帮助或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 引用](https://docs.microsoft.com/rest/api/searchservice/)

