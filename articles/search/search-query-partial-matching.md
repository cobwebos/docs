---
title: 匹配图案和特殊字符
titleSuffix: Azure Cognitive Search
description: 使用通配符和前缀查询在 Azure 认知搜索查询请求中匹配整个或部分术语。 可以使用完整的查询语法和自定义分析器解决包含特殊字符的难以匹配的模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289305"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>匹配图案和特殊字符（破折号）

对于包含特殊字符 （）`-, *, (, ), /, \, =`的查询，或基于较大术语中的部分术语的查询模式，通常需要执行其他配置步骤，以确保索引以正确的格式包含预期内容。 

默认情况下，类似`+1 (425) 703-6214``"1"`的电话号码被标记为 ， `"425"`， `"703"` `"6214"`。 可以想象，在 包含破折`"3-62"`号的部分术语上搜索将失败，因为索引中实际上不存在该内容。 

当您需要搜索部分字符串或特殊字符时，可以使用自定义分析器覆盖默认分析器，该分析器在更简单的标记规则下运行，保留整个术语，当查询字符串包含术语或特殊字符的一部分时，需要保留整个术语字符。 退后一步，该方法如下所示：

+ 选择预定义的分析仪或定义生成所需输出的自定义分析器
+ 将分析器分配给字段
+ 生成索引和测试

本文将引导您完成这些任务。 此处描述的方法在其他方案中很有用：通配符和正则表达式查询也需要整个术语作为模式匹配的基础。 

> [!TIP]
> 评估解释器是一个迭代过程，需要频繁的索引重建。 您可以使用 Postman、[用于创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 REST API、[删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)，使此步骤更加简单。 对于 Load 文档，请求正文应包含要测试的小型代表性数据集（例如，包含电话号码或产品代码的字段）。 使用同一 Postman 集合中的这些 API，您可以快速循环完成这些步骤。

## <a name="choosing-an-analyzer"></a>选择分析仪

选择生成整期令牌的分析器时，以下分析器是常见的选择：

| 分析器 | 行为 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整个字段的内容被标记为单个术语。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 仅在空白上分隔。 包含破折号或其他字符的术语被视为单个令牌。 |
| [自定义分析器](index-add-custom-analyzers.md) | （推荐）通过创建自定义分析器，可以同时指定标记器和令牌筛选器。 以前的分析仪必须原样使用。 自定义分析器允许您选择要使用的标记器和令牌筛选器。 <br><br>建议的组合是具有[小写标记筛选器的](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)[关键字标记器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)。 就其本身而言，预定义的[关键字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)不会小写任何大写文本，这可能导致查询失败。 自定义分析器为您提供了添加小写令牌筛选器的机制。 |

如果使用像 Postman 这样的 Web API 测试工具，则可以添加[测试分析器 REST 调用](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)以检查令牌化输出。 给定现有索引和包含破折号或部分术语的字段，您可以尝试针对特定术语的各种分析器以查看发出的令牌。  

1. 检查标准分析器，了解默认情况下术语如何标记。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 评估响应以查看文本在索引中的标记方式。 请注意每个术语如何小写和分解。

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
1. 修改使用`whitespace`或`keyword`分析器的请求：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 现在，响应由单个令牌（大写）组成，其中保留破折号作为字符串的一部分。 如果需要搜索模式或部分术语，查询引擎现在具有查找匹配项的基础。


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
> 请注意，在生成查询树时，查询解析器通常是搜索表达式中的小写术语。 如果使用的分析器不小写文本输入，并且未获得预期结果，这可能是原因。 解决方案是添加一个 lwower 大小写令牌筛选器。

## <a name="analyzer-definitions"></a>分析器定义
 
无论您是在评估分析器还是推进特定配置，都需要在字段定义上指定分析器，并且如果您不使用内置分析器，则可能需要配置分析器本身。 交换分析器时，通常需要重新生成索引（删除、重新创建和重新加载）。 

### <a name="use-built-in-analyzers"></a>使用内置分析仪

内置或预定义的分析器可以在字段定义的`analyzer`属性上按名称指定，索引中不需要其他配置。 下面的示例演示如何在`whitespace`字段上设置分析器。

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
有关所有可用的内置分析器的详细信息，请参阅[预定义分析器列表](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

### <a name="use-custom-analyzers"></a>使用自定义分析器

如果使用[自定义分析器](index-add-custom-analyzers.md)，请使用用户定义的令牌器、令牌筛选器和可能的配置设置组合在索引中定义它。 接下来，在字段定义上引用它，就像内置分析器一样。

当目标是整个期限标记化时，建议使用由**关键字标记器**和**小写令牌筛选器**组成的自定义分析器。

+ 关键字标记器为字段的整个内容创建单个令牌。
+ 小写令牌筛选器将大写字母转换为小写文本。 查询解析器通常小写任何大写文本输入。 下部同质化使用标记化术语使输入均匀化。

下面的示例演示了提供关键字标记器和小写令牌筛选器的自定义分析器。

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
],

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
> `keyword_v2`系统知道令牌器`lowercase`和令牌筛选器，并使用它们的默认配置，因此无需首先定义它们即可按名称引用它们。

## <a name="tips-and-best-practices"></a>提示和最佳实践

### <a name="tune-query-performance"></a>调整查询性能

如果实现建议配置，包括keyword_v2令牌器和小写令牌筛选器，您可能会注意到查询性能下降，因为索引中现有令牌处理了额外的令牌筛选器。 

下面的示例添加[EdgeNGramTokenFilter，](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)以使前缀匹配得更快。 其他令牌以 2-25 个字符组合生成，包括字符：不仅 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQ、MSFT/SQL）。 可以想象，额外的标记化会导致更大的索引。

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
],

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

分析器在索引期间和查询执行期间调用。 两者使用相同的分析器是很常见的，但您可以为每个工作负载配置自定义分析器。 分析器覆盖在`analyzers`节中的[索引定义](https://docs.microsoft.com/rest/api/searchservice/create-index)中指定，然后引用到特定字段。 

当仅在索引期间需要自定义分析时，您可以将自定义分析器应用于仅索引，并继续使用标准 Lucene 分析器（或其他分析器）进行查询。

要指定特定于角色的分析，可以为每个属性设置字段的属性，设置`indexAnalyzer``searchAnalyzer`而不是默认`analyzer`属性。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>不同方案的重复字段

另一个选项利用每场分析器分配来针对不同的方案进行优化。 具体而言，您可以定义"功能代码"和"功能代码Regex"，以支持在第一个搜索时的常规全文搜索，并在第二个搜索时支持高级模式匹配。

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

本文介绍了分析器如何同时导致查询问题和解决查询问题。 作为下一步，请仔细查看分析器对索引和查询处理的影响。 特别是，请考虑使用分析文本 API 返回标记化输出，以便您可以准确查看分析器为索引创建的内容。

+ [语言分析器](search-language-support.md)
+ [用于 Azure 认知搜索中文本处理的分析器](search-analyzers.md)
+ [分析文本 API （REST）](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文搜索的工作原理（查询体系结构）](search-lucene-query-architecture.md)
