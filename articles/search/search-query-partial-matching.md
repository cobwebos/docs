---
title: 匹配模式和特殊字符
titleSuffix: Azure Cognitive Search
description: 使用通配符和前缀查询来匹配 Azure 认知搜索查询请求中的全部或部分字词。 可以使用完整的查询语法和自定义分析器来解析包含特殊字符的硬匹配模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989613"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>匹配模式和特殊字符（破折号）

对于包含特殊字符（`-, *, (, ), /, \, =`）或基于较大术语中的部分术语的查询模式的查询，通常需要执行其他配置步骤以确保索引包含正确格式的预期内容。 

默认情况下，诸如 `+1 (425) 703-6214` 的电话号码将被标记为 `"1"`、`"425"`、`"703"`、`"6214"`。 正如您所设想的，搜索 `"3-62"`包含短划线的部分术语将会失败，因为该内容并不实际存在于索引中。 

如果需要搜索部分字符串或特殊字符，则可以使用在更简单的标记规则下操作的自定义分析器替代默认分析器，同时保留整个术语，当查询字符串包含部分术语或特殊项时，必要符. 返回一步，该方法如下所示：

+ 选择预定义分析器或定义生成所需输出的自定义分析器
+ 将分析器分配给字段
+ 生成索引和测试

本文将指导你完成这些任务。 此处所述的方法在其他情况下非常有用：通配符和正则表达式查询还需要将整个字词作为模式匹配的基础。 

> [!TIP]
> 评估 analyers 是一个需要频繁重建索引的迭代过程。 可以通过使用 Postman、用于[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 REST api、[删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)来简化此步骤。 对于加载文档，请求正文应包含要测试的小型代表数据集（例如，具有电话号码或产品代码的字段）。 在同一 Postman 集合中使用这些 Api，可以快速循环执行这些步骤。

## <a name="choosing-an-analyzer"></a>选择分析器

选择生成整个术语标记的分析器时，以下分析器是常见选项：

| 分析器 | 行为 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整个字段的内容将被标记为单个字词。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 仅分隔空白区域。 包含短划线或其他字符的字词被视为单个标记。 |
| [自定义分析器](index-add-custom-analyzers.md) | 您创建自定义分析器可同时指定标记器和令牌筛选器。 前面的分析器必须按原样使用。 使用自定义分析器可以选取要使用的 tokenizer 和令牌筛选器。 <br><br>建议结合使用带有[小写令牌筛选器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)的[关键字标记器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)。 预定义的[关键字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)本身并不会将任何大写文本都降低，这可能会导致查询失败。 自定义分析器提供添加小写令牌筛选器的机制。 |

如果使用的是 web API 测试工具（如 Postman），则可以添加[测试分析器 REST 调用](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)来检查标记化的输出。 如果给定了现有索引和包含短划线或部分术语的字段，则可以尝试针对特定术语的各种分析器来查看发出的标记。  

1. 检查标准分析器以查看默认情况下如何标记术语。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 计算响应，查看索引中标记文本的方式。 请注意每个术语如何小写并断开。

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. 修改请求以使用 `whitespace` 或 `keyword` 分析器：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 现在，响应包含单个标记（大写），并以字符串的一部分的形式保留短划线。 如果需要搜索模式或部分术语，则查询引擎现在具有查找匹配项的基础。


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 请注意，生成查询树时，查询分析程序经常搜索表达式中的字词。 如果你使用的分析器不是小写文本输入，并且你没有获得预期结果，则这可能是原因。 解决方案是添加 lwower-case 令牌筛选器。

## <a name="analyzer-definitions"></a>分析器定义
 
无论你是要评估分析器还是要使用特定配置前进，都需要在字段定义中指定分析器，如果未使用内置分析器，则可能还需要配置分析器本身。 交换分析器时，通常需要重新生成索引（删除、重新创建和重新加载）。 

### <a name="use-built-in-analyzers"></a>使用内置分析器

内置或预定义分析器可以按名称在字段定义的 `analyzer` 属性上指定，索引中不需要其他配置。 下面的示例演示如何对字段设置 `whitespace` 分析器。

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```
有关所有可用内置分析器的详细信息，请参阅[预定义分析器列表](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

### <a name="use-custom-analyzers"></a>使用自定义分析器

如果使用的是[自定义分析器](index-add-custom-analyzers.md)，请在索引中使用用户定义的标记器、tokenfilter 和可能的配置设置来定义它。 接下来，在字段定义中引用它，就像内置分析器一样。

如果目标是整术语标记，则建议使用由**关键字标记器**和**小写标记筛选器**组成的自定义分析器。

+ 关键字标记器为字段的全部内容创建单个标记。
+ 小写标记筛选器将大写字母转换为小写的文本。 查询分析器通常是小写的任何大写文本输入。 小写 homogenizes 标记的术语。

下面的示例演示了提供关键字标记器和小写标记筛选器的自定义分析器。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2` 标记器和 `lowercase` 令牌筛选器对于系统是已知的，并且使用其默认配置，这就是你可以按名称引用这些筛选器，而无需首先对其进行定义的原因。

## <a name="tips-and-best-practices"></a>提示和最佳实践

### <a name="tune-query-performance"></a>调整查询性能

如果实现了包含 keyword_v2 标记器和小写标记筛选器的推荐配置，则可能会注意到，查询性能会下降，因为附加的令牌筛选器是通过索引中的现有标记处理的。 

下面的示例添加[EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)以更快地使前缀匹配。 为2-25 个字符组合生成了附加标记，其中包含字符：（不仅 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL）。 您可以想象，附加的词汇切分会导致更大的索引。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>使用不同的分析器进行索引和查询处理

在编制索引期间和执行查询期间调用分析器。 同时为两者使用相同的分析器，但你可以为每个工作负荷配置自定义分析器。 分析器替代在 `analyzers` 部分的[索引定义](https://docs.microsoft.com/rest/api/searchservice/create-index)中指定，然后在特定字段上引用。 

如果在编制索引期间只需要自定义分析，则可以将自定义分析器应用于索引并继续使用标准 Lucene 分析器（或其他分析器）进行查询。

若要指定特定于角色的分析，可以对每个属性的字段设置属性，设置 `indexAnalyzer` 和 `searchAnalyzer` 而不是默认的 `analyzer` 属性。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>不同方案的重复字段

另一种方法是利用每个字段的分析器分配，针对不同的方案进行优化。 具体来说，您可以定义 "featureCode" 和 "featureCodeRegex" 以支持对第一次进行常规全文搜索，在第二种模式下支持高级模式匹配。

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>后续步骤

本文介绍分析器是如何影响查询问题并解决查询问题的。 下一步，详细了解分析器对索引和查询处理的影响。 特别要注意的是，请考虑使用分析文本 API 返回已标记化的输出，以便可以确切地查看分析器为索引创建的内容。

+ [语言分析器](search-language-support.md)
+ [Azure 认知搜索中的文本处理分析器](search-analyzers.md)
+ [分析文本 API （REST）](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文搜索的工作原理（查询体系结构）](search-lucene-query-architecture.md)