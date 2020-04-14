---
title: 模糊搜索
titleSuffix: Azure Cognitive Search
description: 实现"您的意思是"搜索体验，以自动更正拼写错误的术语或拼写错误。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262428"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>模糊搜索以更正拼写错误和拼写错误

Azure 认知搜索支持模糊搜索，这是一种查询类型，用于补偿输入字符串中的拼写错误和拼写错误的术语。 它通过扫描具有类似组合的术语来进行此用。 如果差异只是几个错位的字符，则扩展搜索以覆盖近匹配具有自动更正拼写错误的效果。 

## <a name="what-is-fuzzy-search"></a>什么是模糊搜索？

这是一个扩展练习，在具有类似组成的术语上产生匹配。 指定模糊搜索时，引擎会为查询中的所有术语生成一个图形（基于[确定性有限自动机理论](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)）。 例如，如果查询包含三个术语"Washington 大学"，则为查询`search=university~ of~ washington~`中的每个术语创建一个图形（模糊搜索中没有止步字删除，因此"of"获取一个图形）。

该图由每个术语的最多 50 个扩展或排列组成，捕获流程中的正确和不正确的变体。 然后，引擎返回响应中最相关的匹配项。 

对于像"大学"这样的术语，图表可能有"不合性、大学性、大学性、宇宙性、反数"。 与图形中文档匹配的任何文档都包含在结果中。 与其他分析文本以处理相同单词的不同形式（"mouse"和"Mouse）的查询不同"不同"的查询不同，模糊查询中的比较以面值进行，而不对文本进行任何语言分析。 "宇宙"和"反"在语义上是不同的，将匹配，因为句法差异很小。

如果差异限制为两个或更少的编辑，其中编辑是插入、删除、替换或转换的字符，则匹配将成功。 指定差分的字符串校正算法是[Damerau-Levenshtein 距离](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)指标，描述为"将一个单词更改为另一个单词所需的最小操作数（插入、删除、替换或转位）。 

在 Azure 认知搜索中：

+ 模糊查询适用于整个术语，但您可以通过 AND 构造支持短语。 例如，“Unviersty~ of~ "Wshington~”会与“University of Washington”匹配。

+ 编辑的默认距离为 2。 值`~0`表示没有扩展（仅表示确切术语被视为匹配项），但您可以指定`~1`一个差值或一个编辑。 

+ 模糊查询可以扩展一个术语，最多只能扩展 50 个附加排列。 此限制不可配置，但您可以通过将编辑距离减小到 1 来有效地减少扩展数。

+ 答复包括包含相关匹配的文件（最多 50 份）。

总体而言，图形将作为匹配条件与索引中的令牌提交。 可以想象，模糊搜索本质上比其他查询表单慢。 索引的大小和复杂性可以确定好处是否足以抵消响应的延迟。

> [!NOTE]
> 由于模糊搜索往往很慢，因此可能需要研究替代项，如 n-gram 索引，其短字符序列（bigram 和三字标记的两个和三个字符序列）的进展。 根据您的语言和查询面，n-gram 可能会为您提供更好的性能。 权衡的是，n-gram 索引非常存储密集型，并生成更大的索引。
>
> 另一个替代方案，如果只想处理最恶劣的情况，你可以考虑另一个替代方法，那就是同[义词映射](search-synonyms.md)。 例如，将"搜索"映射到"serach、serch、sarch"或"检索"到"回"。

## <a name="indexing-for-fuzzy-search"></a>模糊搜索的索引

分析器在查询处理期间不用于创建扩展图，但这并不意味着在模糊搜索方案中应忽略分析器。 毕竟，在索引期间，分析器用于创建完成匹配的令牌，查询是自由形式、筛选搜索还是以图形作为输入的模糊搜索。 

通常，当按字段分配分析器时，微调分析链的决定基于主要用例（筛选器或全文搜索），而不是模糊搜索等专用查询表单。 因此，没有针对模糊搜索的特定分析器建议。 

但是，如果测试查询未生成预期的匹配项，则可以尝试更改索引分析器，将其设置为[语言分析器](index-add-language-analyzers.md)，以查看是否获得更好的结果。 某些语言，尤其是元音突变的语言，可以从 Microsoft 自然语言处理器生成的拐点和不规则单词形式中受益。 在某些情况下，使用正确的语言分析器可以改变术语是否以与用户提供的值兼容的方式标记化。

## <a name="how-to-use-fuzzy-search"></a>如何使用模糊搜索

模糊查询使用完整的 Lucene 查询语法构造，调用[Lucene 查询解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

1. 在查询 （）`queryType=full`上设置完整的 Lucene 解析器。

1. 或者，使用此参数 （）`searchFields=<field1,field2>`将请求范围限定为特定字段。 

1. 在整个学期结束时追加波浪线`~`（ ） 运算符 （`search=<string>~`。

   包括一个可选参数，如果要指定编辑距离 （），`~1`则数字介于 0 和 2 之间（默认值）。 例如“blue~”或“blue~1”会返回“blue”、“blues”和“glue”。

在 Azure 认知搜索中，除了术语和距离（最多 2），在查询上没有要设置的其他参数。

> [!NOTE]
> 在查询处理过程中，模糊查询不经过[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 查询输入直接添加到查询树中并展开以创建术语图。 执行的唯一转换是较低的套管。

## <a name="testing-fuzzy-search"></a>测试模糊搜索

对于简单的测试，我们建议[搜索资源管理器](search-explorer.md)或[Postman](search-get-started-postman.md)在查询表达式上迭代。 这两种工具都是交互式的，这意味着您可以快速单步执行术语的多个变体，并评估返回的响应。

当结果不明确时，[点击突出显示](search-pagination-page-layout.md#hit-highlighting)可以帮助您在响应中识别匹配项。 

> [!Note]
> 使用命中突出显示来识别模糊匹配有局限性，仅适用于基本模糊搜索。 如果索引具有评分配置文件，或者使用其他语法对查询进行分层，则点击突出显示可能无法标识匹配项。 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>示例 1：具有确切术语的模糊搜索

假设搜索文档中的`"Description"`字段中存在以下字符串：`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

从"特殊"的模糊搜索开始，并将点击突出显示添加到"描述"字段：

    search=special~&highlight=Description

在响应中，由于添加了命中突出显示，因此格式设置将应用于"特殊"作为匹配项。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

请重试请求，通过取出几个字母（"pe"）：拼写错误"特殊"：

    search=scial~&highlight=Description

到目前为止，反应没有变化。 使用默认的 2 度距离，从"特殊"中删除两个字符"pe"仍然允许该术语成功匹配。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

尝试一个请求，通过删除最后一个字符进行总共三个删除（从"特殊"到"scal"）来进一步修改搜索词：

    search=scal~&highlight=Description

请注意，返回相同的响应，但现在而不是在"特殊"上匹配，模糊匹配位于"SQL"。

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

此扩展示例的要点是说明命中突出显示可能带来的不明确结果的清晰度。 在所有情况下，将返回同一文档。 如果您依赖文档 ID 来验证匹配，则可能错过了从"特殊"到"SQL"的转变。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中全文搜索的工作原理（查询分析体系结构）](search-lucene-query-architecture.md)
+ [搜索资源管理器](search-explorer.md)
+ [如何在 .NET 中进行查询](search-query-dotnet.md)
+ [如何在 REST 中进行查询](search-create-index-rest-api.md)
