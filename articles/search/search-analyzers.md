---
title: 用于语言和文本处理的分析器
titleSuffix: Azure Cognitive Search
description: 将分析器分配到索引中的可搜索文本字段，以将默认标准 Lucene 替换为自定义、预定义或特定语言替代项。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: d7be56fa48887e2ee500f1b253c078bde16d91e6
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891233"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>用于 Azure 认知搜索中文本处理的分析器

分析器是[全文搜索引擎](search-lucene-query-architecture.md)的组成部分，负责在查询字符串和带索引文档中进行文本处理  。 不同的分析器根据具体的方案以不同的方式处理文本。 语言分析器使用语言规则处理文本，以提高搜索质量；其他分析器执行其他基本任务，例如，将字符转换为小写。 

以下视频段快进到如何在 Azure 认知搜索中工作文本处理的说明。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]


语言分析器是用得最多的类型，Azure 认知搜索索引中的每个可搜索字段都分配有默认的语言分析器。 下面是文本分析过程中的典型语言转换：

+ 删除非必需字（非索引字）和标点。
+ 将短语和用连字符连接的词语分解为组成部分。
+ 将大写单词转换为小写单词。
+ 将单词分解为词根形式，以便查找匹配项，而不考虑时态。

语言分析器将文本输入转换为原始形式或词根形式，以快速实现信息存储和信息检索。 在生成索引以编制索引时会发生此转换，在读取索引以执行搜索时会再次进行转换。 如果将同一个分析器用于这两种操作，则更有可能获得所需的搜索结果。

## <a name="default-analyzer"></a>默认分析器  

Azure 认知搜索默认使用 [Apache Lucene 标准分析器 (standard lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)，该分析器按照[“Unicode 文本分段”](https://unicode.org/reports/tr29/)规则将文本分解成多个元素。 此外，标准分析器将所有字符转换为其小写形式。 已编入索引的文档和搜索词在索引和查询处理期间完成分析。  

将会针对每个可搜索字段使用此分析器。 你可以逐字段替代默认值。 替代的分析器可以是[语言分析器](index-add-language-analyzers.md)、[自定义分析器](index-add-custom-analyzers.md)，也可以是[可用分析器列表](index-add-custom-analyzers.md#AnalyzerTable)中的预定义分析器。


## <a name="types-of-analyzers"></a>分析器类型

下表描述了 Azure 认知搜索中可用的分析器。

| 类别 | 说明 |
|----------|-------------|
| [标准 Lucene 分析器](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 默认。 无需任何规范或配置。 这种通用分析器适用于大多数语言和方案。|
| 预定义分析器 | 以成品的形式提供，旨在按原样使用。 <br/>有两种类型：专用和语言特定。 之所以称作“预定义”分析器，是因为它们按名称引用，不需要进行额外的配置或自定义。 <br/><br/>需要对文本输入进行专业处理或最小处理时，请使用[专业（不区分语言）分析器](index-add-custom-analyzers.md#AnalyzerTable)。 非语言预定义分析器包括 Asciifolding、Keyword、Pattern、Simple、Stop 和 Whitespace************************。<br/><br/>当需要为各种语言提供丰富的语言支持时，请使用[语言分析器](index-add-language-analyzers.md)。 Azure 认知搜索支持 35 种 Lucene 语言分析器和 50 种 Microsoft 自然语言处理分析器。 |
|[自定义分析器](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 称为结合了现有元素的用户定义配置，由一个 tokenizer（必需）和可选的筛选器（字符或词元）组成。|

某些预定义分析器（例如 **Pattern** 或 **Stop**）支持有限的一组配置选项。 若要设置这些选项，请有效地创建一个自定义分析器，其中包括某个预定义分析器，以及[预定义分析器参考](index-add-custom-analyzers.md#AnalyzerTable)中所述的一个替代选项。 对于任何自定义配置，请为新配置提供一个名称，例如 *myPatternAnalyzer*，以便将它与 Lucene Pattern 分析器区分开来。

## <a name="how-to-specify-analyzers"></a>如何指定分析器

1. （仅限自定义分析器）在索引定义中创建名为 **analyzer** 的节。 有关详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)和[添加自定义分析器](index-add-custom-analyzers.md)。

2. 在索引中的[字段定义](https://docs.microsoft.com/rest/api/searchservice/create-index)中，将字段的 **analyzer** 属性设置为某个目标分析器的名称（例如 `"analyzer" = "keyword"`。 有效值包括预定义分析器、语言分析器或在索引架构中定义的自定义分析器的名称。 在服务中创建索引之前，请在索引定义阶段规划好分析器的分配。

3. 或者，可以不使用单个 **analyzer** 属性，而使用 **indexAnalyzer** 和 **searchAnalyzer** 字段参数分别设置用于索引和查询的不同分析器。 如果其中的某个活动需要特定的转换，而其他活动不需要该转换，则你可以使用不同的分析器来准备和检索数据。

> [!NOTE]
> 在索引时，不能在索引时使用不同的[语言分析器](index-add-language-analyzers.md)，而不能在查询时使用。 该功能是为[自定义分析器](index-add-custom-analyzers.md)保留的。 因此，如果尝试将 **searchAnalyzer** 或 **indexAnalyzer** 属性设为语言分析器的名称，REST API 将返回错误响应。 必须改用 **analyzer** 属性。

不允许将 **analyzer** 或 **indexAnalyzer** 分配到实际已创建的字段。 如有任何疑问，请查看下表，其中列出了需要重新生成的操作详情以及原因。
 
 | 场景 | 影响 | 步骤 |
 |----------|--------|-------|
 | 添加新字段 | 轻微 | 如果字段尚不存在于架构中，则不需要进行任何字段修订，因为索引中尚不存在字段的物理形式。 可以使用 [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) 将新字段添加到现有索引，使用 [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 来填充字段。|
 | 将 **analyzer** 或 **indexAnalyzer** 添加到现有的已编制索引的字段。 | [过程](search-howto-reindex.md) | 该字段的倒排索引必须从头开始重新创建，并且必须对这些字段的内容重新编制索引。 <br/> <br/>对于正在开发中的索引，[删除](https://docs.microsoft.com/rest/api/searchservice/delete-index)并[创建](https://docs.microsoft.com/rest/api/searchservice/create-index)索引，以获得新的字段定义。 <br/> <br/>对于生产环境中的索引，可以创建一个新字段来提供修改后的定义并开始使用该字段取代旧字段，以推迟重新生成。 使用 [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) 合并新字段，使用 [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 填充该字段。 之后在计划索引服务中，可清除索引以删除过时字段。 |

## <a name="when-to-add-analyzers"></a>何时添加分析器

添加和分配分析器的最佳时机是在开发高潮期，此时，删除和重新创建索引是经常性的操作。

随着索引定义的固化，可将新的分析结构追加到某个索引，但是，若要避免以下错误，需将 **allowIndexDowntime** 标志传递给 [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index)：

*不允许使用索引更新，因为这会导致停机。若要将新的分析器、tokenizer、标记筛选器或字符筛选器添加到现有索引，请将索引更新请求中的 "allowIndexDowntime" 查询参数设置为 "true"。请注意，此操作将使索引脱机至少几秒钟，从而导致索引和查询请求失败。索引更新后，可能会在数分钟内削弱索引的性能和写入可用性，或更长的索引。 "*

将分析器分配到某个字段时也是如此。 分析器是字段定义的不可或缺部分，因此，请只在创建字段时才添加分析器。 若要将分析器添加到现有字段，必须[删除并重新生成](search-howto-reindex.md)索引，或者添加包含所需分析器的新字段。

如前所述，一种例外情况是 **searchAnalyzer** 变体。 在指定分析器的三种方法（**analyzer**、**indexAnalyzer**、**searchAnalyzer**）中，只有 **searchAnalyzer** 属性能够在现有字段中更改。

## <a name="recommendations-for-working-with-analyzers"></a>有关使用分析器的建议

本部分提供分析器使用方法的相关建议。

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>除非特别要求，否则读写操作使用一个分析器

Azure 认知搜索允许通过附加的 **indexAnalyzer** 和 **searchAnalyzer** 字段参数来指定使用不同的分析器执行索引和搜索。 如果未指定，则使用 **analyzer** 属性设置的分析器将用于索引编制和搜索。 如果未指定 `analyzer`，将使用默认标准 Lucene 分析器。

除非特别要求，否则索引和查询一般使用同一个分析器。 请务必全面测试。 如果搜索和索引时的文本处理不同，则当搜索和索引分析器配置不一致时，查询词和索引词可能会不匹配。

### <a name="test-during-active-development"></a>在活动开发中进行测试

替换标准分析器需要重新生成索引。 如果可能，请先决定在活动开发中使用的分析器，然后再将索引应用到生产中。

### <a name="inspect-tokenized-terms"></a>检查已标记的词语

如果搜索未能返回所需的结果，最有可能的情况是查询上的词语输入和索引中已标记的词语之间存在标记差异。 如果标记不同，匹配则无法具体化。 若要检查 tokenizer 输出，建议将 [Analyze API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) 用作调查工具。 响应包含令牌，由特定分析器生成。

<a name="examples"></a>

## <a name="rest-examples"></a>REST 示例

下方示例演示了几个主要方案的分析器定义。

+ [自定义分析器示例](#Custom-analyzer-example)
+ [将分析器分配到字段的示例](#Per-field-analyzer-assignment-example)
+ [混合用于索引和搜索操作的分析器](#Mixing-analyzers-for-indexing-and-search-operations)
+ [语言分析器示例](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>自定义分析器示例

本示例说明了具有自定义选项的分析器定义。 字符筛选器、分词器和词元筛选器的自定义选项分别指定为命名构造，然后在分析器定义中引用。 预定义元素按原样使用，并可通过名称轻松引用。

分析此示例：

* 分析器是可搜索字段的字段类的属性。
* 自定义分析器是索引定义的一部分。 它支持轻度自定义（例如，在某个筛选器中自定义一个单独选项）或在多个位置自定义。
* 在这种情况下，自定义分析器为“my_analyzer”，它将反过来使用自定义的标准分词器“my_standard_tokenizer”和两个词元筛选器：小写的自定义 asciifolding 筛选器“my_asciifolding”。
* 它还定义了 2 个自定义字符型筛选器“map_dash”和“remove_whitespace”。 第一个使用下划线替换所有破折号，而第二个用于删除所有空格。 空间需要在映射规则中进行 UTF-8 编码。 字符型筛选器在标记化之前进行应用并将影响生成的标记（标准 tokenizer 在破折号和空格上中断，但不会在下划线上中断）。

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
              "map_dash",
              "remove_whitespace"
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
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
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

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>每个字段的分析器分配示例

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

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>混合用于索引和搜索操作的分析器

API 包括为索引和搜索指定不同分析器的其他索引属性。 必须以对的形式指定 **searchAnalyzer** 和 **indexAnalyzer** 属性，并替换单个 **analyzer** 属性。


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

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>语言分析器示例

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

## <a name="c-examples"></a>C# 示例

如果使用 .NET SDK 代码示例，则可追加这些示例，以便使用或配置分析器。

+ [分配内置分析器](#Assign-a-language-analyzer)
+ [配置分析器](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>分配语言分析器

任何按原样使用且没有任何配置的分析器都是在字段定义中指定的。 没有创建分析器构造的要求。 

此示例将 Microsoft 英语和法语分析器分配给说明字段。 它是从更大的酒店索引定义中提取的代码片段，使用 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 示例的 hotels.cs 文件中的酒店类进行创建。

调用[分析器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)，指定 [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) 类型，提供在 Azure 认知搜索中受支持的文本分析器。

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>定义自定义分析器

如果需要自定义或配置，则需向索引添加分析器构造。 定义以后，即可将其添加到字段定义，如上一示例所示。

创建 [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) 对象。 如需更多示例，请参阅 [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs)。

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>后续步骤

+ 请参阅 [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)获取全面的说明。 本文通过示例来说明某些表面上看起来与预期不符的行为。

+ 通过[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples)示例部分或者通过门户中“搜索资源管理器”的[简单查询语法](query-simple-syntax.md)尝试其他查询语法。

+ 了解如何应用[语言特定的词法分析器](index-add-language-analyzers.md)。

+ [配置自定义分析器](index-add-custom-analyzers.md)，针对单个字段尽量简化处理或者进行专门处理。

## <a name="see-also"></a>另请参阅

 [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [简化的查询语法](query-simple-syntax.md) 

 [完整 Lucene 查询语法](query-lucene-syntax.md) 
 
 [处理搜索结果](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
