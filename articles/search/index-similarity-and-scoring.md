---
title: 相似性和评分概述
titleSuffix: Azure Cognitive Search
description: 介绍相似性和评分的概念，以及开发人员可以执行哪些操作来自定义评分结果。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 4b02039c86f43e6bebed58dfff475816f09a3da1
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890142"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure 认知搜索中的相似性和评分

评分是指在全文搜索查询的搜索结果中返回的每个项的搜索评分。 分数指示某一项在当前搜索操作上下文中的相关性。 分数越高，项的相关度就越高。 在搜索结果中，根据为每项计算的搜索分数，按从高到低的顺序排列各项。 

默认情况下，将在响应中返回前50，但你可以使用 **$top**参数返回更小或更大的项（单个响应中最多1000个），并 **$skip**以获取下一组结果。

根据数据和查询的统计属性计算搜索分数。 Azure 认知搜索查找与搜索词匹配的文档（部分或全部，具体取决于[searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)），favoring 包含多个搜索词实例的文档。 如果搜索词在数据索引中很少见，但在文档中很常见，搜索分数仍升至更高。 这种计算关联方法的基础称为*TF-IDF 或*术语 "频率-反转文档频率"。

搜索分数值可以在整个结果集中重复。 当多个命中具有相同的搜索分数时，未定义相同评分项的顺序，也不稳定。 再次运行查询，你可能会看到项偏移位置，尤其是在使用免费服务或具有多个副本的计费服务时。 对于具有相同分数的两项，无法保证先显示哪一个。

如果要打破重复分数之间的关联，可以将 **$orderby**子句添加到第一个按分数排序，然后按另一个可排序字段排序（例如`$orderby=search.score() desc,Rating desc`）。 有关详细信息，请参阅[$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby)。

> [!NOTE]
> `@search.score = 1.00`指示未评分或未排名的结果集。 分数在所有结果中都是统一的。 查询窗体为模糊搜索、通配符或正则表达式或 **$filter**表达式时，将出现未评分的结果。 

## <a name="scoring-profiles"></a>为配置文件评分

通过定义自定义*计分配置文件*，可以自定义不同字段的排序方式。 借助计分概要文件，可以更好地控制搜索结果中的项排名。 例如，建议根据创收能力提升项、提升新项或提升库存时间太长的项。 

计分概要文件属于索引定义的一部分，由加权字段、函数和参数组成。 有关定义它的详细信息，请参阅[计分配置文件](index-add-scoring-profiles.md)。

## <a name="scoring-statistics"></a>评分统计信息

为了实现可伸缩性，Azure 认知搜索通过分片进程水平分布每个索引，这意味着索引的部分以物理方式分开。

默认情况下，将根据*分片中*数据的统计属性计算文档的分数。 这种方法通常并不是大语料库数据的问题，因此，它可以提供比基于所有分片的信息计算分数更好的性能。 也就是说，如果最终在不同的分片中，则使用这种性能优化可能会导致两个非常相似的文档（甚至是相同的文档）得到不同的相关性分数。

如果希望根据所有分片中的统计属性计算分数，可以通过将*scoringStatistics = global*添加为[查询参数](https://docs.microsoft.com/rest/api/searchservice/search-documents)（或将 *"scoringStatistics"： "global"* 添加为[查询请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)的主体参数）来计算分数。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> `scoringStatistics`参数需要管理 api 密钥。

## <a name="similarity-ranking-algorithms"></a>相似性排名算法

Azure 认知搜索支持两种不同的相似性排名算法：*典型的相似性*算法和*Okapi BM25*算法的正式实现（目前为预览版）。 传统相似性算法是默认算法，但从7月15日开始，在该日期之后创建的任何新服务都将使用新的 BM25 算法。 它将是新服务中唯一可用的算法。

现在，你可以指定要使用的相似性排名算法。 有关详细信息，请参阅[排名算法](index-ranking-similarity.md)。

以下视频段快进到 Azure 认知搜索中使用的排名算法的说明。 可以观看完整的视频以获得更多背景。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="see-also"></a>另请参阅

 [计分配置文件](index-add-scoring-profiles.md) [REST API 参考](https://docs.microsoft.com/rest/api/searchservice/)   
 [搜索文档 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Azure 认知搜索 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
