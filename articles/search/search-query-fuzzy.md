---
title: 模糊搜索
titleSuffix: Azure Cognitive Search
description: 实现 "您的意思是" 搜索体验，以自动更正拼写错误的词或拼写错误。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262428"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>模糊搜索，纠正拼写错误和拼写错误

Azure 认知搜索支持模糊搜索，这是一种查询类型，可补偿输入字符串中的拼写错误和拼写错误的字词。 它通过扫描具有类似组合的术语来实现此功能。 将搜索范围扩大到涵盖近匹配效果，可以在差异只是放错了几个字符时自动更正拼写错误。 

## <a name="what-is-fuzzy-search"></a>什么是模糊搜索？

这是一项展开练习，它生成的匹配项具有类似的组合。 当指定了模糊搜索时，引擎将为查询中的所有词生成一个类似于组合字词的关系图（基于[确定性有限自动机](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)）。 例如，如果您的查询包括三个条款 "华盛顿大学"，则会为查询`search=university~ of~ washington~`中的每个术语创建一个图形（模糊搜索中不会删除任何删除词，因此 "of" 会获得图形）。

关系图包含每个术语的最多50个扩展或排列，同时捕获过程中的正确和不正确的变体。 然后，引擎将在响应中返回最顶层的相关匹配项。 

对于 "大学" 这样的术语，图形可能有 "unversty，universty，大学，universe，反转"。 与图表中的那些匹配的任何文档都包括在结果中。 与分析文本以处理同一词的不同形式（"鼠标" 和 "鼠标"）的其他查询不同，模糊查询中的比较是以表面上的，而不是对文本进行任何语言分析。 "Universe" 和 "反转" （在语义上是不同的）将匹配，因为语法差异很小。

如果差异限制为两个或更少的编辑，则匹配成功，其中，编辑是插入、删除、替换或转换后的字符。 指定差异的字符串更正算法是[Damerau-Levenshtein 距离](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)指标，描述为 "将一个词更改为其他单词所需的最小操作数目（插入、删除、替换或 transpositions 两个相邻字符）"。 

在 Azure 中认知搜索：

+ 模糊查询适用于整个术语，但你可以通过和构造支持短语。 例如，“Unviersty~ of~ "Wshington~”会与“University of Washington”匹配。

+ 编辑的默认距离为2。 值为`~0`表示无展开（仅将确切的字词视为匹配项），但您可以指定`~1`一种不同的值或一个编辑。 

+ 模糊查询最多可以扩展到50个附加排列的术语。 此限制不可配置，但你可以通过将编辑距离减小到1来有效地减少扩展的数目。

+ 响应由包含相关匹配项的文档（最多50）组成。

对于索引中的标记，关系图将提交为匹配条件。 正如您所设想的，模糊搜索在本质上比其他查询窗体更慢。 索引的大小和复杂性可以确定是否足以抵消响应的延迟。

> [!NOTE]
> 由于模糊搜索的速度较慢，因此，可能有必要调查一些替代项（如 n 语法索引）及其短字符序列（bigram 和三个三个字符序列）的进度。 根据你的语言和查询图面，n 语法可能会为你提供更好的性能。 要权衡的一点是，n 元语法索引是非常耗时的存储，并生成更大的索引。
>
> 另一种方法是，如果您只想处理最严重的情况，则可以考虑[同义词映射](search-synonyms.md)。 例如，将 "search" 映射到 "搜索，serch，sarch" 或 "检索" 以 "检索"。

## <a name="indexing-for-fuzzy-search"></a>模糊搜索的索引

在查询处理过程中不使用分析器来创建扩展关系图，但这并不意味着在模糊搜索方案中应忽略分析器。 毕竟，在编制索引期间使用分析器来创建对其执行匹配的标记，该查询是免费形式、筛选的搜索还是使用图形作为输入的模糊搜索。 

通常，在每个字段上分配分析器时，对分析链进行微调的决定都基于主用例（筛选器或全文搜索），而不是基于模糊搜索等专用查询窗体。 出于此原因，没有特定的 analyzer 建议用于模糊搜索。 

但是，如果测试查询没有生成所需的匹配项，可以尝试改变索引分析器，将其设置为[语言分析器](index-add-language-analyzers.md)，查看是否获得更好的结果。 某些语言（尤其是具有元音突变的语言）可从 Microsoft 自然语言处理器生成的转折点和不规则单词窗体中获益。 在某些情况下，使用正确的语言分析器可以使术语是否被标记为与用户提供的值兼容的方式。

## <a name="how-to-use-fuzzy-search"></a>如何使用模糊搜索

模糊查询是使用完整的 Lucene 查询语法构造的，调用了[lucene 查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

1. 在查询中设置完整的 Lucene 分析器（`queryType=full`）。

1. （可选）使用此参数（`searchFields=<field1,field2>`）将请求的范围限定为特定字段。 

1. 在整个字词`search=<string>~`的`~`末尾追加波形符（）。

   如果要指定编辑距离（`~1`），请在0到2（默认值）之间包含一个可选参数。 例如“blue~”或“blue~1”会返回“blue”、“blues”和“glue”。

在 Azure 认知搜索中，除了术语和距离（最多为2）以外，还没有其他参数可对查询进行设置。

> [!NOTE]
> 在查询处理期间，模糊查询不进行[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 查询输入会直接添加到查询树中并进行扩展，以创建字词关系图。 执行的唯一转换是较低的大小写。

## <a name="testing-fuzzy-search"></a>测试模糊搜索

对于简单的测试，建议使用[搜索资源管理器](search-explorer.md)或[Postman](search-get-started-postman.md)来循环访问查询表达式。 这两种工具都是交互式的，这意味着你可以快速遍历某个术语的多个变体，并评估返回的响应。

如果结果不明确，[命中突出显示](search-pagination-page-layout.md#hit-highlighting)可帮助你在响应中标识匹配项。 

> [!Note]
> 使用命中突出显示来识别模糊匹配具有局限性，仅适用于基本的模糊搜索。 如果索引具有计分配置文件，或者将查询与其他语法进行分层，则命中突出显示可能无法识别匹配项。 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>示例1：用精确字词进行模糊搜索

假设搜索文档中的某个`"Description"`字段存在以下字符串：`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

使用 "特殊" 的模糊搜索开始，并在说明字段中添加命中突出显示：

    search=special~&highlight=Description

在响应中，由于添加了命中突出显示，因此，格式设置将应用于 "特殊" 作为匹配术语。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

通过使用几个字母（"pe"），再次尝试请求，拼写错误 "特殊"：

    search=scial~&highlight=Description

到目前为止，不更改响应。 使用2度距离的默认值，从 "特殊" 中删除两个字符 "pe" 仍允许成功匹配该字词。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

尝试执行一个请求，通过将最后一个字符（从 "特殊" 到 "比例"）取出一个最后一个字符，进一步修改搜索词：

    search=scal~&highlight=Description

请注意，返回了相同的响应，但现在不是 "特殊" 的匹配项，模糊匹配项在 "SQL" 中。

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

此扩展示例的要点是说明命中突出显示可能会产生不明确结果的清晰度。 在所有情况下，都会返回相同的文档。 如果依赖文档 Id 来验证匹配项，则可能已错过了从 "特殊" 到 "SQL" 的转换。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中全文搜索的工作原理（查询分析体系结构）](search-lucene-query-architecture.md)
+ [搜索资源管理器](search-explorer.md)
+ [如何在 .NET 中进行查询](search-query-dotnet.md)
+ [如何在 REST 中进行查询](search-create-index-rest-api.md)
