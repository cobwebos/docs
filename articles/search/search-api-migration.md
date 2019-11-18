---
title: 升级 REST API 版本
titleSuffix: Azure Cognitive Search
description: 查看 API 版本中的差异并了解将现有代码迁移到最新的 Azure 认知搜索服务 REST API 版本所需的操作。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112167"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>升级到最新的 Azure 认知搜索服务 REST API 版本

如果你使用的是早期版本的[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)，本文将帮助你升级应用程序以使用最新的已正式发布的 API 版本2019-05-06。

REST API 版本 2019-05-06 包含某些针对早期版本进行的更改。 这些更改主要涉及向后兼容性，因此更改代码只需最小的工作量，具体取决于之前使用的是哪个版本。 [升级步骤](#UpgradeSteps)概述了使用新功能所要做出的代码更改。

> [!NOTE]
> Azure 认知搜索服务实例支持一系列 REST API 版本，包括早期版本。 你可以继续使用这些 API 版本，但我们建议将代码迁移到最新版本，以便可以访问新功能。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>版本 2019-05-06 中的新增功能
版本2019-05-06 是 REST API 的最新公开发布版本。 此 API 版本中已正式发布的功能包括：

* [自动完成](index-add-suggesters.md)是一项自动提示功能，可以完成部分指定的字词输入。

* [复杂类型](search-howto-complex-data-types.md)为搜索索引中的结构化对象数据提供本机支持。

* [JsonLines 分析模式](search-howto-index-json-blobs.md)（Azure Blob 编制索引的一部分）可为每个 JSON 实体创建以换行符分隔的搜索文档。

* [Ai 扩充](cognitive-search-concept-intro.md)提供的索引利用了认知服务的 AI 扩充引擎。

有多个预览版功能版本与此正式版更新相一致。 若要查看新的预览版功能列表，请参阅[搜索 REST API 版本 2019-05-06-Preview](search-api-preview.md)。

## <a name="breaking-changes"></a>重大变化

在 api-version=2019-05-06 中，包含以下功能的现有代码将会中断。

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB - 数据源的索引器现在是 "type": "cosmosdb"

如果使用 [Cosmos DB 索引器](search-howto-index-cosmosdb.md )，必须将 `"type": "documentdb"` 更改为 `"type": "cosmosdb"`。

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>索引器执行结果错误不再提供状态

索引器执行的错误结构以前包含 `status` 元素。 此元素已被删除，因为它不能提供有用的信息。

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>索引器数据源 API 不再返回连接字符串

从 API 版本 2019-05-06 和 2019-05-06-Preview 开始，数据源 API 不再在任何 REST 操作的响应中返回连接字符串。 在以前的 API 版本中，对于使用 POST 创建的数据源，Azure 认知搜索返回**201** ，后跟 OData 响应，其中包含纯文本形式的连接字符串。

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>“命名实体识别”认知技能现已停用

如果在代码中调用[命名实体识别](cognitive-search-skill-named-entity-recognition.md)技能，调用将会失败。 替代的功能是[实体识别](cognitive-search-skill-entity-recognition.md)。 无需进行任何其他更改应该就能替换技能引用。 两个版本的 API 签名相同。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤
如果从以前的正式版 2017-11-11 或 2016-09-01 升级，除了更改版本号，可能无需对代码进行任何其他更改。 仅对于以下情况，可能需要更改代码：

* 当 API 响应中返回无法识别的属性时，代码失效。 默认情况下，应用程序应忽略无法理解的属性。

* 代码仍坚持 API 请求，并尝试将其重新发送到新 API 版本。 例如，如果应用程序仍存留从搜索 API 返回的延续标记（有关详细信息，请查找`@search.nextPageParameters`搜索 API 参考[中的 ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)）。

如果任一种情况适用，都可能需要更改相应代码。 否则，如果不是要开始使用版本 2019-05-06 的[新功能](#WhatsNew)，便无需进行任何更改。

如果从预览版 API 升级，上述内容同样适用，但还必须注意，某些预览功能并未在版本 2019-05-06 中提供：

* [“More like this”查询](search-more-like-this.md)
* [CSV Blob 索引编制](search-howto-index-csv-blobs.md)
* [对 Cosmos DB 索引器的 MongoDB API 支持](search-howto-index-cosmosdb.md)

如果代码使用这些功能，只有消除它们的使用，才能升级到 API 版本 2019-05-06。

> [!IMPORTANT]
> 预览 API 用于测试和评估，不应在生产环境中使用。
> 

### <a name="upgrading-complex-types"></a>升级复杂类型

如果代码使用在 2017-11-11-Preview 或 2016-09-01-Preview 旧版 API 中创建的复杂类型，需要注意版本 2019-05-06 中存在一些新的和已更改的限制：

+ 每个索引的子字段深度和复杂集合数目限制已降低。 如果使用预览版 API 创建了超出这些限制的索引，尝试使用 API 版本 2019-05-06 更新或重新创建这些索引将会失败。 如果遇到这种情况，需要重新设计架构，使其不会超出新的限制，然后重新生成索引。

+ API 版本 2019-05-06 对每个文档的复杂集合元素数目施加了新的限制。 如果使用预览版 API 创建了文档超出这些限制的索引，尝试使用 API 版本 2019-05-06 重建该数据的索引将会失败。 如果遇到这种情况，需要在重建数据的索引之前，减少每个文档的复杂集合元素数目。

有关详细信息，请参阅[Azure 认知搜索的服务限制](search-limits-quotas-capacity.md)。

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>如何升级旧的复杂类型结构

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

查看搜索 REST API 参考文档。 如果遇到问题，请通过 [StackOverflow](https://stackoverflow.com/) 向我们寻求帮助，或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 参考](https://docs.microsoft.com/rest/api/searchservice/)

