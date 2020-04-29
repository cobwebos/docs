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
ms.openlocfilehash: 1975c13162316b4132bae34659b1c5af8e416573
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231605"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure 认知搜索中的排名算法

> [!IMPORTANT]
> 从2020年7月15日起，新创建的搜索服务将自动使用 BM25 排名函数，这在大多数情况下已经过证实，可提供与当前默认排名更好的用户期望的搜索排名。 除了卓越的排名准确度以外，BM25 还启用了根据文档大小等因素优化结果的配置选项。  
>
> 由于此项变更，你很可能会发现搜索结果的顺序稍有变化。 对于想要测试此更改影响的用户，可以在 api 版本 2019-05-06-Preview 中使用 BM25 算法。  

本文介绍如何在现有搜索服务上使用新的 BM25 排名算法，以获取使用预览 API 创建和查询的新索引。

Azure 认知搜索正在采用 Okapi BM25 算法*BM25Similarity*的官方 Lucene 实现，这将替换以前使用过的*ClassicSimilarity*实现。 与较旧的 ClassicSimilarity 算法一样，BM25Similarity 是一个类似 TF 的检索函数，它使用术语 frequency （TF）和反转文档频率（IDF）作为变量来计算每个文档查询对的相关性分数，然后将其用于排名。 

虽然 BM25 在概念上类似于旧式“经典相似性”算法，但它立足于概率信息检索来改善结果。 BM25 还提供高级自定义选项，例如，允许用户确定如何根据匹配字词的字词频率调整相关性评分。

## <a name="how-to-test-bm25-today"></a>如何立即测试 BM25

创建新索引时，可以设置一个**相似性**属性来指定算法。 需要使用`api-version=2019-05-06-Preview`，如下所示。

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

当这两种算法仅适用于现有服务时，"**相似性**" 属性在此过渡期内非常有用。 

| properties | 描述 |
|----------|-------------|
| 相似性 | 可选。 有效的值包括 *#Microsoft "ClassicSimilarity"* 或 *#Microsoft "BM25Similarity"* 的值。 <br/> 在`api-version=2019-05-06-Preview` 2020 年7月15日之前创建的搜索服务要求或更高版本。 |

对于2020年7月15日之后创建的新服务，BM25 将自动使用，并且是唯一的相似性算法。 如果尝试`ClassicSimilarity`在新服务上设置 "**相似性**"，则将返回400错误，因为新服务不支持该算法。

对于在2020年7月15日之前创建的现有服务，经典相似性仍为默认算法。 如果省略**相似性**属性或将其设置为 null，则索引将使用经典算法。 如果要使用新算法，则需要按如上所述设置**相似性**。

## <a name="bm25-similarity-parameters"></a>BM25 相似性参数

BM25 相似性添加了两个用户可自定义参数，用于控制计算的相关性分数。

### <a name="k1"></a>k1

*版 k1*参数控制每个匹配字词的字词频率与文档查询对的最终关联分数之间的缩放功能。

如果值为零，则表示 "二进制模型"，其中，对于所有匹配的文档，单个匹配术语的贡献是相同的，而与该字词在文本中出现的次数相同，而较大的版 k1 值允许分数继续增加，因为在文档中找到了同一术语的更多实例。 默认情况下，Azure 认知搜索将版 k1 参数的值设置为1.2。 如果希望将多个字词作为搜索查询的一部分，则使用较高的版 k1 值可能很重要。 在这些情况下，我们可能希望使用与多个不同查询词匹配的文档，这些搜索词只匹配单个文档，多次匹配。 例如，在查询包含术语 "Apollo Spaceflight" 的文档的索引时，我们可能希望将包含术语 "Apollo" 的一篇文章的分数降低了几次，而不提及 "Spaceflight"，而另一篇文章只是显式提到了 "Apollo" 和 "Spaceflight"。 
 
### <a name="b"></a>b

*B*参数控制文档长度对相关性分数的影响。

如果值为0.0，则表示文档的长度不会影响分数，而1.0 值则表示术语对相关性分数的影响将按文档的长度进行规范化。 用于 b 参数的 Azure 认知搜索中使用的默认值是0.75。 在需要妨碍较长的文档的情况下，规范化术语 "频率" 在文档长度上非常有用。 在某些情况下，与更短的文档相比，较长的文档（例如完整的 novel）更有可能包含很多不相关的术语。

### <a name="setting-k1-and-b-parameters"></a>设置版 k1 和 b 参数

若要自定义 b 或版 k1 值，只需在使用 BM25 时将其作为属性添加到相似性对象：

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

相似性算法只能在创建索引时设置。 这意味着不能更改现有索引使用的相似性算法。 当更新使用 BM25 的现有索引定义时，可以修改 *"b"* 和 *"版 k1"* 参数。 更改现有索引的这些值将使索引脱机至少几秒钟，从而导致索引和查询请求失败。 因此，你将需要在更新请求的查询字符串中设置 "allowIndexDowntime = true" 参数：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>请参阅  

+ [REST API 引用](https://docs.microsoft.com/rest/api/searchservice/)   
+ [向索引添加计分配置文件](index-add-scoring-profiles.md)    
+ [创建索引 API](https://docs.microsoft.com/rest/api/searchservice/create-index)   
+ [Azure 认知搜索 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
