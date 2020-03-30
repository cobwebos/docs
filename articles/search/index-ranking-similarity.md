---
title: 排名相似性算法
titleSuffix: Azure Cognitive Search
description: 如何设置相似性算法以尝试新的相似性算法进行排名
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409968"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure 认知搜索中的排名算法

> [!IMPORTANT]
> 从 2020 年 7 月 15 日起，新创建的搜索服务将使用 BM25 排名功能，在大多数情况下，该功能已证明可提供与用户期望更好的搜索排名，而不是当前的默认排名。  除了卓越的排名之外，BM25 还支持基于文档大小等因素调整结果的配置选项。  
>
> 有了此更改，您很可能会看到搜索结果的顺序略有变化。   对于那些想要测试此更改的影响的用户，我们在 2019-05-06-预览 API 中提供了启用 BM25 在新索引上评分的能力。  

本文介绍如何更新 2020 年 7 月 15 日之前创建的服务，以使用新的 BM25 排名算法。

Azure 认知搜索将使用 Okapi BM25 算法*BM25 相似性*的官方 Lucene 实现，该算法将取代以前使用*的经典相似性*实现。 与较旧的经典相似性算法一样，BM25相似性是一种类似于TF-IDF的检索函数，它使用术语频率 （TF） 和反向文档频率 （IDF） 作为变量来计算每个文档查询对的相关性分数，然后计算用于排名。 虽然BM25在概念上与旧的经典相似性算法相似，但它在概率信息检索中具有根，以改进它。 BM25 还提供高级自定义选项，例如允许用户决定相关性分数如何与匹配术语的术语频率一起缩放。

## <a name="how-to-test-bm25-today"></a>今天如何测试BM25

创建新索引时，可以设置"相似性"属性。 您需要使用*2019-05-06 预览*版，如下所示。

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

对于在 2020 年 7 月 15 日之前创建的服务：如果省略相似性或设置为 null，则索引将使用旧的经典相似性算法。

对于 2020 年 7 月 15 日之后创建的服务：如果省略相似性或设置为 null，则索引将使用新的 BM25 相似性算法。

您还可以显式将相似性值设置为以下两个值之一 *："#Microsoft.Azure.Search.Classic.经典相似性"* 或 *"#Microsoft.Azure.Search.BM25 相似性"。*


## <a name="bm25-similarity-parameters"></a>BM25 相似性参数

BM25 相似性添加了两个用户可自定义的参数来控制计算的相关性分数：

### <a name="k1"></a>k1

*k1*参数控制每个匹配术语的术语频率与文档查询对的最终相关性分数之间的缩放函数。

值零表示"二进制模型"，其中单个匹配项的贡献对于所有匹配文档都是相同的，而该术语在文本中出现的次数如何，而较大的 k1 值允许分数继续随着更多时间增加而增加同一术语的实例在文档中找到。 默认情况下，Azure 认知搜索对 k1 参数使用值 1.2。 在我们希望多个术语成为搜索查询的一部分的情况下，使用较高的 k1 值可能很重要。 在这些情况下，我们可能希望支持与搜索的许多不同的查询词匹配的文档，这些查询词仅与单个查询词匹配多次。 例如，在查询包含术语"阿波罗太空飞行"的文档索引时，我们可能希望降低一篇关于希腊神话的文章的分数，该文章包含"阿波罗"一词几十次，而没有提到"太空飞行"，与另一篇文章，明确提到"阿波罗"和"太空飞行"只有几次。 
 
### <a name="b"></a>b

*b*参数控制文档的长度如何影响相关性分数。

值 0.0 表示文档的长度不会影响分数，而值 1.0 表示术语频率对相关性分数的影响将按文档的长度进行规范化。 Azure 认知搜索 b 参数使用的默认值为 0.75。 在我们想要惩罚较长的文档的情况下，按文档长度将术语频率规范化非常有用。 在某些情况下，与较短的文档相比，较长的文档（如完整的小说）更有可能包含许多不相关的术语。

### <a name="setting-k1-and-b-parameters"></a>设置 k1 和 b 参数

要自定义 b 或 k1 值，只需将它们作为属性添加到相似性对象时，使用 BM25：

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

相似性算法只能在索引创建时设置。 这意味着不能更改现有索引所使用的相似性算法。 在更新使用 BM25 的现有索引定义时，可以修改 *"b"* 和 *"k1"* 参数。 更改现有索引上的这些值将使索引脱机至少几秒钟，从而导致索引和查询请求失败。 因此，您需要在更新请求的查询字符串中设置"allowIndexDowntime_true"参数：

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>请参阅  

 [Azure 认知搜索 REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [将评分配置文件添加到索引](index-add-scoring-profiles.md)    
 [创建索引&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure 认知搜索 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
