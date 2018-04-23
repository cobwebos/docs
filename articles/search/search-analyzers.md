---
title: Azure 搜索中的分析器 | Microsoft Docs
description: 将分析器分配到索引中的可搜索文本字段，以将默认标准 Lucene 替换为自定义、预定义或特定语言替代项。
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.openlocfilehash: e858966fb5a15b84af1952399a5eff3ca50d0d59
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="analyzers-in-azure-search"></a>Azure 搜索中的分析器

“分析器”是[全文搜索](search-lucene-query-architecture.md)的组成部分，负责在查询字符串和带索引文档中进行文本处理。 以下是分析过程中的典型转换：

+ 删除非必需字（非索引字）和标点。
+ 将短语和用连字符连接的词语分解为组成部分。
+ 将大写单词转换为小写单词。
+ 将单词分解为词根形式，以便查找匹配项，而不考虑时态。

语言分析器将文本输入转换为原始形式或词根形式，以快速实现信息存储和信息检索。 在生成索引以编制索引时会发生此转换，在读取索引以执行搜索时会再次进行转换。 如果将同一个文本分析器用于这两种操作，则更有可能获得所需的搜索结果。

Azure 搜索使用[标准 Lucene 分析器](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)作为默认值。 你可以逐字段替代默认值。 本文介绍了选项范围并提供了用于自定义分析的最佳做法。 它还提供了用于主要方案的配置示例。

## <a name="supported-analyzers"></a>支持的分析器

下表介绍了 Azure 搜索支持的分析器。

| 类别 | 说明 |
|----------|-------------|
| [标准 Lucene 分析器](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 默认。 无需任何规范或配置。 这种通用分析器适用于大多数语言和方案。|
| 预定义分析器 | 作为按原样使用且具有有限自定义项的成品提供。 <br/>有两种类型：专用和语言特定。 由于按名称而非自定义项引用，因此被称为“预定义”。 <br/><br/>当需要对文本输入进行专业处理或最小处理时，请使用[专业（不限语言）分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)。 非语言预定义分析器包括 Asciifolding、Keyword、Pattern、Simple、Stop 和 Whitespace。<br/><br/>当需要为各种语言提供丰富的语言支持时，请使用[语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)。 Azure 搜索支持 35 种 Lucene 语言分析器和 50 种 Microsoft 自然语言处理分析器。 |
|[自定义分析器](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 一种结合了现有元素的用户定义配置，由一个分词器（必需）和可选的筛选器（字符或词元）组成。|

可将预定义分析器（如 Pattern 或 Stop）自定义为使用[预定义分析器引用](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)中所述的替换选项。 可供设置选项的预定义分析器只有几个。 对于任何自定义项，请为你的新配置提供一个名称，例如 myPatternAnalyzer，以便将其与 Lucene Pattern 分析器区别开。

## <a name="how-to-specify-analyzers"></a>如何指定分析器

1. （仅限自定义分析器）在索引定义中创建一个 **analyzer** 部分。 有关详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)及[自定义分析器 > 创建](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer)。

2. 在索引中的[字段定义](https://docs.microsoft.com/rest/api/searchservice/create-index)中，将 **analyzer** 属性设置为某个目标分析器的名称（例如 `"analyzer" = "keyword"`）。 有效值包括预定义分析器、语言分析器或在索引架构中定义的自定义分析器的名称。

3. 还可以不使用单个 **analyzer** 属性，而使用 **indexAnalyzer** 和 **searchAnalyzer** 字段参数分别设置用于索引和查询的不同分析器。 

3. 向字段定义中添加分析器会在索引上引发写入操作。 如果向现有索引中添加**分析器**，请注意以下步骤：
 
 | 场景 | 影响 | 步骤 |
 |----------|--------|-------|
 | 添加新字段 | 轻微 | 如果字段尚不存在于架构中，则不需要进行任何字段修订，因为索引中尚不存在字段的物理形式。 使用[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)和 [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 处理此任务。|
 | 向现有索引字段添加分析器。 | 重新生成 | 该字段的倒排索引必须从头开始重新创建，并且必须对这些字段的内容重新编制索引。 <br/> <br/>对于正在开发中的索引，[删除](https://docs.microsoft.com/rest/api/searchservice/delete-index)并[创建](https://docs.microsoft.com/rest/api/searchservice/create-index)索引，以获得新的字段定义。 <br/> <br/>对于正在生产中的索引，应创建一个新字段来提供修改后的定义并开始使用该字段。 使用[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)和 [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 以包含新字段。 之后在计划索引服务中，可清除索引以删除过时字段。 |

## <a name="tips-and-best-practices"></a>提示和最佳做法

本部分提供分析器使用方法的相关建议。

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>除非特别要求，否则读写操作使用一个分析器

Azure 搜索允许通过附加的 `indexAnalyzer` 和 `searchAnalyzer` 字段参数来指定使用不同的分析器执行索引和搜索。 如果未指定，使用设置分析器`analyzer`属性用于索引编制和搜索。 如果未指定 `analyzer`，将使用默认标准 Lucene 分析器。

除非特别要求，否则索引和查询一般使用同一个分析器。 请务必全面测试。 如果搜索和索引时的文本处理不同，则当搜索和索引分析器配置不一致时，查询词和索引词可能会不匹配。

### <a name="test-during-active-development"></a>在活动开发中进行测试

替换标准分析器需要重新生成索引。 如果可能，请先决定在活动开发中使用的分析器，然后再将索引应用到生产中。

### <a name="inspect-tokenized-terms"></a>检查已标记的词语

如果搜索未能返回所需的结果，最有可能的情况是查询上的词语输入和索引中已标记的词语之间存在标记差异。 如果标记不同，匹配则无法具体化。 若要检查 tokenizer 输出，建议将 [Analyze API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) 用作调查工具。 响应包含令牌，由特定分析器生成。

### <a name="compare-english-analyzers"></a>比较英语分析器

[搜索分析器演示](http://alice.unearth.ai/)是第三方演示应用，它演示了对标准 Lucene 分析器、Lucene 英语分析器和 Microsoft 英语自然语言处理器的并排比较。 索引是固定的；它包含常见情景中的文本。 对于提供的每个搜索输入，每个分析器的结果将显示在相邻窗格中，使你了解每个分析器是如何处理同一个字符串的。 

## <a name="examples"></a>示例

下方示例演示了几个主要方案的分析器定义。

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>示例 1：自定义选项

本示例说明了具有自定义选项的分析器定义。 字符筛选器、分词器和词元筛选器的自定义选项分别指定为命名构造，然后在分析器定义中引用。 预定义元素按原样使用，并可通过名称轻松引用。

分析此示例：

* 分析器是可搜索字段的字段类的属性。
* 自定义分析器是索引定义的一部分。 它支持轻度自定义（例如，在某个筛选器中自定义一个单独选项）或在多个位置自定义。
* 在这种情况下，自定义分析器为“my_analyzer”，它将反过来使用自定义的标准分词器“my_standard_tokenizer”和两个词元筛选器：小写的自定义 asciifolding 筛选器“my_asciifolding”。
* 它还定义一个自定义“map_dash”字符筛选器，以便在词汇切分前将所有短划线替换为下划线（标准分词器在短划线而非下划线上中断）。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>示例 2：替代默认分析器

默认为标准分析器。 假设你希望将默认分析器替换为其他预定义分析器（如 Pattern 分析器）。 如果尚未设置自定义选项，则只需在字段定义中通过名称来指定它。

“分析器”元素逐字段替代标准分析器。 不能全局替代。 在本例中，`text1` 使用 Pattern 分析器和 `text2`，它不指定分析器，仅使用默认值。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>示例 3：用于索引和搜索操作的不同分析器

API 包括为索引和搜索指定不同分析器的其他索引属性。 `searchAnalyzer` 和 `indexAnalyzer` 属性必须成对指定，并替换单个 `analyzer` 属性。


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>示例 4：语言分析器

包含其他语言字符串的字段可使用语言分析器，而其他字段将保留默认值（或使用某些其他预定义或自定义分析器）。 如果使用语言分析器，则必须将其同时用于索引和搜索操作。 使用语言分析器的字段不得对索引和搜索使用不同的分析器。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>后续步骤

+ 请参阅 [Azure 搜索中全文搜索的工作原理](search-lucene-query-architecture.md)获取全面的说明。 本文通过示例来说明某些表面上看起来与预期不符的行为。

+ 通过[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples)示例部分或者通过门户中“搜索资源管理器”的[简单查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)尝试其他查询语法。

+ 了解如何应用[语言特定的词法分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)。

+ [配置自定义分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)，针对单个字段尽量简化处理或者进行专门处理。

+ 在此演示网站的相邻窗格中[比较标准和英语分析器](http://alice.unearth.ai/)。 

## <a name="see-also"></a>另请参阅

 [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [完整 Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [处理搜索结果](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
