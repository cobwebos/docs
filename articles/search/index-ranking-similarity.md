---
title: 排名相似性算法
titleSuffix: Azure Cognitive Search
description: 如何设置相似性算法，以试用新的相似性算法进行排名
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: 651e0635f0b556cd47adfccdbac59ef587570128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535723"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure 认知搜索中的排名算法

> [!IMPORTANT]
> 从 2020 年 7 月 15 日起，新创建的搜索服务将自动使用 BM25 排名函数，经证实，在大多数情况下，该函数提供的搜索排名优于当前默认排名，可以更好地达到用户的期望。 除了卓越的排名准确度以外，BM25 还启用了根据文档大小等因素优化结果的配置选项。  
>
> 由于此项变更，你很可能会发现搜索结果的顺序稍有变化。 对于想要测试此项变更带来的影响的用户，可以使用 api-version 2019-05-06-Preview 和 2020-06-30 中的 BM25 算法。  

本文介绍如何在现有搜索服务上，对使用预览版 API 创建和查询的新索引使用新的 BM25 排名算法。

Azure 认知搜索正在使用 Okapi BM25 算法的官方 Lucene 实现 (BM25Similarity)，该实现将取代以前使用的 ClassicSimilarity 实现 。 与旧式 ClassicSimilarity 算法一样，BM25Similarity 是类似于 TF-IDF 的检索函数，它使用字词频率 (TF) 和逆向文档频率 (IDF) 作为变量来计算每个文档-查询对的相关性评分，然后使用这些评分进行排名。 

虽然 BM25 在概念上类似于旧式“经典相似性”算法，但它立足于概率信息检索来改善结果。 BM25 还提供高级自定义选项，例如，允许用户确定如何根据匹配字词的字词频率调整相关性评分。

## <a name="how-to-test-bm25-today"></a>如何立即测试 BM25

创建新索引时，可以设置 similarity 属性来指定该算法。 可以使用 `api-version=2019-05-06-Preview`（如下所示）或 `api-version=2020-06-30`。

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

当两种算法都可用时，similarity 属性在此过渡期间仅在现有服务上有用。 

| 属性 | 说明 |
|----------|-------------|
| similarity | 可选。 有效值包括“#Microsoft.Azure.Search.ClassicSimilarity”或“#Microsoft.Azure.Search.BM25Similarity”。 <br/> 对于 2020 年 7 月 15 日之前创建的搜索服务，需要安装 `api-version=2019-05-06-Preview` 或更高版本。 |

对于 2020 年 7 月 15 日之后创建的新服务，将自动使用 BM25，并且这是唯一的相似性算法。 如果尝试在新服务上将 similarity 设置为 `ClassicSimilarity`，则会返回 400 错误，因为新服务不支持该算法。

对于 2020 年 7 月 15 日之前创建的现有服务，经典相似性仍然是默认算法。 如果 similarity 属性被省略或设置为 null，则索引将使用经典算法。 如果要使用新算法，则需要如上所述设置 similarity。

## <a name="bm25-similarity-parameters"></a>BM25 相似性参数

BM25 相似性添加了两个用户可自定义参数来控制计算出的相关性评分。

### <a name="k1"></a>k1

k1 参数控制每个匹配字词的字词频率与文档-查询对的最终相关性评分之间的调整函数。

零值表示“二元模型”，其中，单个匹配字词的贡献对于所有匹配文档是相同的，而不管该字词在文本中出现多少次；使用较大的 k1 值可让评分随着在文档中找到同一字词的更多实例而不断提高。 默认情况下，Azure 认知搜索对 k1 参数使用值 1.2。 如果我们预期要在搜索查询中包含多个字词，则使用较大的 k1 值可能很重要。 在这种情况下，我们可能倾向于使用与多个不同的搜索查询字词匹配的文档，而不使用只与单个字词匹配多次的文档。 例如，在索引中查询包含字词“阿波罗宇宙飞行”的文档时，面对一篇有关希腊神话的、几十次提到字词“阿波罗”但未提到“宇宙飞行”的文章，以及一篇明确提到了字词“阿波罗”和“宇宙飞行”，但只是提到了几次的文章，我们可能希望降低前一篇文章的评分。 
 
### <a name="b"></a>b

b 参数控制文档长度如何影响相关性评分。

值 0.0 表示文档的长度不会影响评分，而值 1.0 则表示将按文档长度规范化字词频率对相关性评分的影响。 Azure 认知搜索中对 b 参数使用的默认值为 0.75。 如果我们想要惩罚较长的文档，则按文档长度规范化字词频率会很有用。 在某些情况下，与短得多的文档相比，较长的文档（例如整篇小说）更有可能包含很多不相关的字词。

### <a name="setting-k1-and-b-parameters"></a>设置 k1 和 b 参数

若要自定义 b 或 k1 值，只需在使用 BM25 时将其作为属性添加到 similarity 对象：

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

只能在创建索引时设置相似性算法。 这意味着，无法对现有索引更改正在使用的相似性算法。 可以在更新使用 BM25 的现有索引定义时修改“b”和“k1”参数。  更改现有索引的这些值会使索引至少脱机几秒钟，导致索引和查询请求失败。 因此，需要在更新请求的查询字符串中设置“allowIndexDowntime=true”参数：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>另请参阅  

+ [REST API 参考](/rest/api/searchservice/)
+ [将计分概要文件添加到索引](index-add-scoring-profiles.md)
+ [创建索引 API](/rest/api/searchservice/create-index)
+ [Azure 认知搜索 .NET SDK](/dotnet/api/overview/azure/search)