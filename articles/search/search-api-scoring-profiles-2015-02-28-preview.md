---
title: "计分概要文件（Azure 搜索 REST API 版本 2015-02-28-Preview）| Microsoft Docs"
description: "Azure 搜索是一种托管云搜索服务，支持根据用户定义的计分概要文件微调排名结果。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: bfd62649-13d7-40b3-a8fa-85521a15084d
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.author: heidist
ms.date: 10/27/2016
ms.openlocfilehash: a67637d149a84313270c03d21acf8a9c1870be05
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>计分概要文件（Azure 搜索 REST API 版本 2015-02-28-Preview）
> [!NOTE]
> 本文介绍了 [2015-02-28-Preview](search-api-2015-02-28-preview.md) 中的计分概要文件。 [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) 上记录的 `2016-09-01` 版本与此处所述的 `2015-02-28-Preview` 版本当前之间没有任何差别，但我们仍提供本文档，以使文档内容覆盖整个 API。
>
>

## <a name="overview"></a>概述
计分是指搜索结果中所返回每项的搜索分数计算。 分数指示某一项在当前搜索操作上下文中的相关性。 分数越高，项的相关度就越高。 在搜索结果中，根据为每项计算的搜索分数，按从高到低的顺序排列项。

Azure 搜索使用默认计分计算初始分数，但可以通过计分概要文件自定义计算。 评分配置文件为在搜索结果更好地控制项的排名。 例如，你可能想要提升项基于营收潜力、 提升较新的项，或可能是提升了库存时间太长的项。

计分概要文件属于索引定义的一部分，由字段、函数和参数组成。

若要向你提供了解评分配置文件如下所示，下面的示例演示了一个称为地域的简单配置文件。 此文件用于提升在 `hotelName` 字段中具有搜索词的项。 它还使用 `distance` 函数优先提升在当前位置十公里范围内的项。 如果有人搜索“inn”一词，而“inn”恰好是酒店名称的一部分，包含带有“inn”的酒店的文档会在搜索结果中的较高位置出现。

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

若要使用此计分概要文件，查询要表述为对查询字符串指定此概要文件。 在下面的查询中，请注意请求中的查询参数 `scoringProfile=geo`。

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

此查询对“inn”一词进行搜索，并在当前位置中传递。 请注意，此查询包含其他参数，如 `scoringParameter`。 查询参数在[搜索文档（Azure 搜索 API）](search-api-2015-02-28-preview.md#SearchDocs)中介绍。

单击[示例](#example)查看计分概要文件的更详细示例。

## <a name="what-is-default-scoring"></a>什么是默认计分？
计分对排序结果集中的每项计算搜索分数。 搜索结果集中的每项都分配到一个搜索分数，并从高到低排名。 分数较高的项返回应用程序。 默认返回前 50 个，但可以使用 `$top` 参数返回较少或较多的项（单个响应中最多 1000 个）。

默认情况下，根据数据和查询的统计属性计算搜索分数。 Azure 搜索查找包含了查询字符串中搜索词的文档（部分或全部包含，具体取决于 `searchMode`），优先列出包含该搜索词多个实例的文档。 如果搜索词在数据语料库中很少见，但在文档中很常见，搜索分数仍升至更高。 这种相关性计算方法的基础称为 TF-IDF（词频-逆文档频率）。

假设没有自定义排序，结果在返回调用应用程序之前按搜索分数排名。 如果 `$top` 未指定，则返回具有最高搜索分数的 50 项。

搜索分数值可以在整个结果集中重复。 例如，你可能具有 10 项评分为 1.2，20 个项的分数为 1.0 和 20 个项的分数为 0.5。 当多个命中具有相同的搜索分数时，得分相同项的顺序未定义，并且不稳定。 重新运行查询后，可能会看到项移位。 对于具有相同分数的两项，无法保证先显示哪一个。

## <a name="when-to-use-custom-scoring"></a>何时使用自定义计分
当默认排名行为不足以满足业务目标时，应创建一个或更多计分概要文件。 例如，你可能决定搜索相关性应该倾向于新添加的项。 同样，可能有一个包含利润率的字段，或其他某些指示营收潜力的字段。 提升为业务带来益处的命中可能是确定使用计分概要文件的一个重要因素。

基于相关性的排序也通过计分概要文件实现。 请考虑过去使用的可以按价格、日期、评分或相关性排序的搜索结果页。 在 Azure 搜索中，计分概要文件驱动“相关性”选项。 相关性定义由你控制，基于业务目标和希望提供的搜索体验类型断定。

<a name="example"></a>

## <a name="example"></a>示例
如前所述，自定义计分通过索引架构中定义的计分概要文件实现。

此示例演示具有两个计分概要文件的索引架构（`boostGenre`、`newAndHighlyRated`）。 针对此索引的任何查询（包含任一概要文件作为查询参数）将使用此概要文件对结果集进行计分。

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>工作流
要实现自定义计分行为，请将计分概要文件添加到定义该索引的架构。 在一个索引中可以具有最多 16 个计分概要文件（请参阅[服务限制](search-limits-quotas-capacity.md)），但在任何给定查询中，一次都只能指定一个概要文件。

从本主题中提供的[模板](#bkmk_template)开始。

提供一个名称。 计分概要文件为可选项，但如果要添加一个概要文件，必须提供名称。 请务必遵循字段的命名约定（以字母开头，避免使用特殊字符和保留字）。 请参阅[命名规则](http://msdn.microsoft.com/library/azure/dn857353.aspx)获取更多信息。

计分概要文件的主体由加权字段和函数构造而成。

### <a name="weights"></a>权重
计分概要文件的 `weights` 属性指定向字段分配相对权重的名称-值对。 在[示例](#example)中，albumTitle、genre 和 artistName 字段各自提升 1.5、5 和 2。 genre 为何比其他字段提升更高？ 如果对在某种程度上为同类的数据执行搜索（正如 `musicstoreindex` 中的“genre”一样），可能需要在相对权重中产生较大差异。 例如，在 `musicstoreindex` 中，“rock”既作为流派显示，又显示在采用相同组句方式的流派说明中。 如果希望流派的权重在流派说明之上，genre 字段将需要更高的相对权重。

### <a name="functions"></a>函数
函数在特定上下文需要其他计算时使用。 有效的函数类型为 `freshness`、`magnitude`、`distance` 和 `tag`。 每个函数具有独有的参数。

* 当希望按照新旧程度提升项时，应使用 `freshness`。 此函数仅可与 datetime 字段 (`Edm.DataTimeOffset`) 结合使用。 请注意 `boostingDuration` 特性仅用于 freshness 函数。
* 当希望根据数值高低程度提升时，应使用 `magnitude`。 调用此函数的方案包括按照利润率、最高价格、最低价格或下载次数提升。 如果想要反转模式（例如，将价格较低项提升至价格较高项之上），可以将范围反转为从高到低。 假设价格范围从 100 美元到 1 美元，可以将 `boostingRangeStart` 设为 100、`boostingRangeEnd` 设为 1 以提升价格较低的项。 此函数仅可与 double 和 integer 字段结合使用。
* 当希望按距离或地理位置提升时，应使用 `distance`。 此函数仅可与 `Edm.GeographyPoint` 字段结合使用。
* 当希望按文档和搜索查询之间共有的标记提升时，应使用 `tag`。 此函数仅可与 `Edm.String` 和 `Collection(Edm.String)` 字段结合使用。

#### <a name="rules-for-using-functions"></a>使用函数的规则
* 函数类型（freshness、magnitude、distance、tag）必须是小写。
* 函数不能包含 null 或空值。 具体而言，如果包含字段名称，则必须将其设置为某值。
* 函数仅可应用于可筛选字段。 请参阅[创建索引](search-api-2015-02-28-preview.md#CreateIndex)，了解有关可筛选字段的更多信息。
* 函数仅可应用于在索引的字段集合中定义的字段。

定义索引后，请通过上传索引架构（后跟文档）建立索引。 请参阅[创建索引](search-api-2015-02-28-preview.md#CreateIndex)和[添加或更新文档](search-api-2015-02-28-preview.md#AddOrUpdateDocuments)，获取有关这些操作的说明。 建立索引后，应获得适用于搜索数据的函数计分概要文件。

<a name="bkmk_template"></a>

## <a name="template"></a>模板
本部分演示计分概要文件的语法和模板。 请参阅下一部分的[索引特性参考](#bkmk_indexref)，获取特性的说明。

    ...
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>

## <a name="scoring-profile-property-reference"></a>计分概要文件属性参考
> [!NOTE]
> 计分函数仅可应用于可筛选的字段。
>
>

| 属性 | 说明 |
| --- | --- |
| `name` |必需。 这是计分概要文件的名称。 它遵循与字段相同的命名约定。 它必须以字母开头，不得包含圆点、冒号或 @ 符号，不得以短语“azureSearch”（区分大小写）开头。 |
| `text` |包含 Weights 属性。 |
| `weights` |可选。 指定字段名称和相对权重的名称-值对。 相对权重必须为正整数或浮点数。 可以指定不带相应权重的字段名称。 权重用于指示一个字段相对于另一个字段的重要性。 |
| `functions` |可选。 请注意，计分函数仅可应用于可筛选字段。 |
| `type` |计分函数的必需项。 指示要使用的函数类型。 有效值包括 `magnitude`、`freshness`、`distance` 和 `tag`。 可以在每个计分概要文件中包含多个函数。 函数名称必须小写。 |
| `boost` |计分函数的必需项。 用作原始分数乘数的正数。 不得等于 1。 |
| `fieldName` |计分函数的必需项。 计分函数仅可应用于作为索引字段集合一部分且可筛选的字段。 此外，每个函数类型都引入了其他限制（freshness 与 datetime 字段结合使用、magnitude 与 integer 或 double 字段结合使用、distance 与 location 字段结合使用，而 tag 与 string 或 string 集合字段结合使用）。 仅可按函数定义指定单个字段。 例如，若要在同一概要文件中使用两次 magnitude，则需要包含两个定义 magnitude，每个字段一个。 |
| `interpolation` |计分函数的必需项。 定义从范围起始至范围结束的分数提升增量的斜率。 有效值包括 `linear`（默认）、`constant`、`quadratic` 和 `logarithmic`。 请参阅[设置插值](#bkmk_interpolation)获取详细信息。 |
| `magnitude` |magnitude 计分函数用于改变基于数值字段的值范围的排名。 一些最常见的用法示例如下：<ul><li>星级评分：改变基于“Star Rating”字段内的值的计分。 如果两个项相关，具有较高评分的项先显示。</li><li>利润：当两个文档相关时，零售商可能希望先提升具有较高利润的文档。</li><li>点击次数：对于跟踪产品或页面点击行为的应用程序，可使用 magnitude 提升容易获得最多流量的项。</li><li>下载次数：对于跟踪下载的应用程序，magnitude 函数可提升下载次数最多的项。</li></ul> |
| `magnitude:boostingRangeStart` |设置对其进行量值计分的范围的起始值。 该值必须是整数或浮点数。 对于星级评分 1 到 4，这里应为 1。 对于超过 50% 的利润率，这里应为 50。 |
| `magnitude:boostingRangeEnd` |设置对其进行量值计分的范围的结束值。 该值必须是整数或浮点数。 对于星级评分 1 到 4，这里应为 4。 |
| `magnitude:constantBoostBeyondRange` |有效值为 true 或 false（默认）。 设置为 true 时，完整的提升将继续应用到有一个目标字段值高于范围上限的文档。 如果为 false，此函数的提升不会应用到有一个目标字段值超出范围的文档。 |
| `freshness` |freshness 计分函数用于改变基于 DateTimeOffset 字段值的项的排名分数。 例如，具有较新日期的项可以排在日期较旧的项之上。 （请注意，也可以排列日历事件等具有未来日期的项，以便接近当前日期的项可以排在距离当前日期较远的将来的项之上。）在当前服务版本中，范围的一端将固定为当前时间。 另一端是基于 `boostingDuration` 的过去的时间。 要提升将来时间的范围，请使用负 `boostingDuration`。 提升从最大范围和最小范围改变的比率由应用到计分概要文件的内插确定（请见下图）。 若要反转应用的提升系数，请选择不超过 1 的提升系数。 |
| `freshness:boostingDuration` |设置一个有效期，超过这个有效期之后，针对特定文档的 Boosting 将停止。 请参阅以下语法和示例部分中的[设置 boostingDuration](#bkmk_boostdur)。 |
| `distance` |距离计分函数用于影响基于其相对的参考地理位置远近程度的文档分数。 参考位置在参数中指定为查询的一部分（使用 `scoringParameter` 查询参数），作为经纬度实参。 |
| `distance:referencePointParameter` |要在查询中传递以用作参考位置的参数。 scoringParameter 是一个查询参数。 请参阅[搜索文档](search-api-2015-02-28-preview.md#SearchDocs)获取查询参数的说明。 |
| `distance:boostingDistance` |以公里为单位指示与参考位置（提升范围结束）之间距离的数字。 |
| `tag` |tag 计分函数用于影响基于文档和搜索查询中标记的文档的分数。 将提升与搜索查询有共同标记的文档。 搜索查询的标记作为每个搜索请求中的计分参数提供（使用 `scoringParameter` 查询参数）。 |
| `tag:tagsParameter` |要在查询中传递以指定特定请求标记的参数。 `scoringParameter` 是一个查询参数。 请参阅[搜索文档](search-api-2015-02-28-preview.md#SearchDocs)获取查询参数的说明。 |
| `functionAggregation` |可选。 仅在函数已指定时适用。 有效值包括：`sum`（默认）、`average`、`minimum`、`maximum` 和 `firstMatching`。 搜索分数是从多个变量（包括多个函数）中计算的单个值。 此特性指示所有函数的提升如何组合到随后应用到基本文档分数的单个聚合提升中。 基本分数基于从文档和搜索查询计算得出的 tf-idf 值。 |
| `defaultScoringProfile` |在执行搜索请求时，如果未指定任何计分概要文件，则使用默认计分（仅限 tf-idf）。 可以在此处设置默认计分概要文件名称，以使 Azure 搜索在搜索请求中未给定任何特定概要文件时使用默认概要文件。 |

<a name="bkmk_interpolation"></a>

## <a name="set-interpolations"></a>设置内插
内插允许定义从范围起始至范围结束的计分提升增量的斜率。 可以使用以下内插：

* `Linear`：对于最大和最小范围内的项，应用于项的提升以恒定递减量完成。 Linear 是计分概要文件的默认内插。
* `Constant`：对于起始和结束范围内的项，将对排名结果应用恒定提升。
* `Quadratic`：与具有恒定递减提升的 Linear 内插相比，Quadratic 最初以较小的速度递减，并在接近结束范围时，以明显更高的间隔递减。 tag 计分函数中不允许使用此内插选项。
* `Logarithmic`：与具有恒定递减提升的 Linear 内插相比，Logarithmic 最初以较大的速度递减，并在接近结束范围时，以明显更小的间隔递减。 tag 计分函数中不允许使用此内插选项。

<a name="Figure1"></a> ![][1]

<a name="bkmk_boostdur"></a>

## <a name="set-boostingduration"></a>设置 boostingDuration
`boostingDuration` 是 freshness 函数的特性。 使用它设置一个有效期，超过这个有效期之后，针对特定文档的提升将停止。 例如，要在 10 天促销期内提升某个产品系列或品牌，应针对这些文档将 10 天期限指定为 "P10D"。 或者要在下周提升即将举办的活动，则指定 "-P7D"。

`boostingDuration` 必须设置为 XSD "dayTimeDuration" 值（ISO 8601 持续时间值的受限子集）的格式。 它的模式为：`[-]P[nD][T[nH][nM][nS]]`。

下表提供几个示例。

| 持续时间 | boostingDuration |
| --- | --- |
| 1 天 |"P1D" |
| 2 天 12 小时 |"P2DT12H" |
| 15 分钟 |"PT15M" |
| 30 天 5 小时 10 分钟 6.334 秒 |"P30DT5H10M6.334S" |

有关更多示例，请参阅 [XML 架构：数据类型（W3.org 网站）](http://www.w3.org/TR/xmlschema11-2/)。

**另请参阅**
MSDN 上的 [Azure 搜索服务 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) <br/>MSDN 上的
[创建索引（Azure 搜索 API）](http://msdn.microsoft.com/library/azure/dn798941.aspx)<br/>MSDN 上的
[将计分概要文件添加到搜索索引](http://msdn.microsoft.com/library/azure/dn798928.aspx)<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
