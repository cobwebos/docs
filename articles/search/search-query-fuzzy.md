---
title: 模糊搜索
titleSuffix: Azure Cognitive Search
description: 实现“你是不是指”搜索体验，以自动更正拼错的字词或拼写错误。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81262428"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>使用模糊搜索来更正拼写错误

Azure 认知搜索支持模糊搜索 - 这是可以纠正输入字符串中的拼写错误和拼错的字词的一种查询。 它通过扫描具有类似构成部分的字词来实现此功能。 当差异只是体现在几个字符放错了位置时，扩展搜索以包括近似匹配项能够对自动更正拼写错误产生影响。 

## <a name="what-is-fuzzy-search"></a>什么是模糊搜索？

模糊搜索是一种扩展措施，可以根据具有类似构成部分的字词生成匹配项。 当指定了模糊搜索时，引擎将为查询中的所有词生成一个类似于组合字词的关系图（基于[确定性有限自动机](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)）。 例如，如果查询包含三个字词“university of washington”，则会为查询 `search=university~ of~ washington~` 中的每个字词创建一张图（模糊搜索中不会删除非索引字，因此“of”也会获得一张图）。

图中最多包含每个字词的 50 个扩展（或排列），在过程中捕获正确和错误的变体。 然后，引擎在响应中返回最相关的匹配项。 

对于类似于“university”的字词，图中可能包含“unversty”、“universty”、“university”、“universe”、“inverse”。 与图中的字词匹配的任何文档会包含在结果中。 与对文本进行分析来处理同一单词的不同形式（“mice”和“mouse”）的其他查询不同，模糊查询中的比较是针对表面值进行的，不会对文本进行任何语言分析。 语义不同的“universe”和“inverse”相互匹配，因为两者的句法差异很小。

如果差异限制为两个或更少的编辑（其中的编辑是指插入、删除、替换或转换的字符），则匹配成功。 指定差异的字符串更正算法是[Damerau-Levenshtein 距离](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)指标，描述为 "将一个词更改为其他单词所需的最小操作数目（插入、删除、替换或 transpositions 两个相邻字符）"。 

在 Azure 认知搜索中：

+ 模糊查询将应用于整个字词，但你可以通过 AND 构造来支持短语。 例如，“Unviersty~ of~ "Wshington~”会与“University of Washington”匹配。

+ 默认的编辑距离为 2。 值 `~0` 表示无扩展（仅将完全相同的字词视为匹配项），但你可以指定 `~1` 表示一个差异程度或一个编辑。 

+ 模糊查询可将一个字词扩展到最多 50 个附加排列。 此限制不可配置，但通过将编辑距离减至 1 可以有效减少扩展数目。

+ 响应由包含相关匹配项的文档（最多 50 个）组成。

图是作为针对索引中的标记的匹配条件统一提交的。 可以想象得出，模糊搜索的速度固有地比其他查询形式要慢。 索引的大小和复杂性可以决定带来的优势是否足以抵消响应的延迟。

> [!NOTE]
> 由于模糊搜索的速度往往较慢，因此，可能有必要探索 n 元语法索引等采用短字符序列（由两到三个字符组成的双元和三元标记序列）的替代方案。 n 元语法可能会提高性能，具体取决于所用的语言和查询面。 弊端是 n 元语法索引非常耗费存储，且生成的索引要大得多。
>
> 如果你只是想要处理最极端的情况，另一种替代方案是使用[同义词映射](search-synonyms.md)。 例如，将“search”映射到“serach, serch, sarch”，或者将“retrieve”映射到“retreive”。

## <a name="indexing-for-fuzzy-search"></a>模糊搜索的索引编制

在查询处理过程中不会使用分析器来创建扩展图，但这并不意味着在模糊搜索方案中应该忽略分析器。 毕竟，在索引编制过程中将使用分析器来创建进行匹配时所依据的标记，不管查询是自由形式、筛选搜索还是使用图作为输入的模糊搜索。 

通常，当基于每个字段分配分析器时，将基于主要用例（筛选器或全文搜索）而不是基于模糊搜索等专用搜索形式来做出微调分析链的决策。 因此，对于模糊搜索，没有具体的分析器建议。 

但是，如果测试查询没有生成预期的匹配项，你可以尝试改变索引分析器，将其设置为[语言分析器](index-add-language-analyzers.md)，以查看是否获得了更好的结果。 某些语言（尤其是存在元音变化的语言）可以受益于 Microsoft 自然语言处理器生成的音调变化和不规则单词形式。 在某些情况下，使用正确的语言分析器可能关系到术语是否能够以与用户提供的值兼容的方式进行标记化。

## <a name="how-to-use-fuzzy-search"></a>如何使用模糊搜索

模糊查询是使用完整 Lucene 查询语法构造的，它调用 [Lucene 查询分析程序](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

1. 在查询中设置完整的 Lucene 分析程序 (`queryType=full`)。

1. （可选）使用此参数将请求范围限定为特定的字段 (`searchFields=<field1,field2>`)。 

1. 将波浪符 (`~`) 运算符追加到完整字词的末尾 (`search=<string>~`)。

   若要指定编辑距离，请包含一个可选参数，其值介于 0 和 2（默认值）之间 (`~1`)。 例如“blue~”或“blue~1”会返回“blue”、“blues”和“glue”。

在 Azure 认知搜索中，除了字词和距离（最大为 2）以外，不需要在查询中设置其他参数。

> [!NOTE]
> 在处理查询期间，模糊查询不会进行[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 查询输入会直接添加到查询树中，并进行扩展以创建字词图。 执行的唯一变换是将字词小写。

## <a name="testing-fuzzy-search"></a>测试模糊搜索

要进行简单测试，我们建议使用[搜索浏览器](search-explorer.md)或 [Postman](search-get-started-postman.md) 来循环访问查询表达式。 这两个工具都是交互式的，这意味着，可以快速分步测试某个字词的多个变体，并评估返回的响应。

当结果模糊时，[命中项突出显示](search-pagination-page-layout.md#hit-highlighting)可以帮助你识别响应中的匹配项。 

> [!Note]
> 使用命中项突出显示识别模糊匹配项存在局限性，仅适用于基本的模糊搜索。 如果索引具有评分配置文件，或者使用附加语法将查询分层，则命中项突出显示可能无法识别匹配项。 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>示例 1：对完全匹配的字词进行模糊搜索

假设某个搜索文档的 `"Description"` 字段中存在以下字符串：`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

首先对“special”执行模糊搜索，并向 Description 字段添加命中项突出显示：

    search=special~&highlight=Description

在响应中，由于添加了命中项突出显示，因此格式设置将应用于作为匹配字词的“special”。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

删掉“special”中的几个字母（“pe”）以将其误拼，然后再次尝试该请求：

    search=scial~&highlight=Description

到目前为止，响应没有任何变化。 使用默认的 2 度距离时，从“special”中删除两个字符“pe”后，仍可成功匹配该字词。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

再尝试一次请求，这次请如下所述进一步修改搜索字词：删除最后一个字符，也就是总共删除三个字符（使“special”变成“scal”）：

    search=scal~&highlight=Description

可以看到，返回了相同的响应，但匹配不是针对“special”进行的，而是针对“SQL”进行了模糊匹配。

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

此扩展示例的要点是演示命中项突出显示可为模糊结果带来的明确性。 在所有情况下，都将返回同一文档。 如果你依赖于文档 ID 来验证匹配，则可能会漏掉从“special”到“SQL”的变动。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中全文搜索的工作原理（查询分析体系结构）](search-lucene-query-architecture.md)
+ [搜索资源管理器](search-explorer.md)
+ [如何在 .NET 中进行查询](search-query-dotnet.md)
+ [如何在 REST 中进行查询](search-create-index-rest-api.md)
