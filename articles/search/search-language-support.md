---
title: 用于非英语搜索查询的多语言索引
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索支持 56 种语言，通过 Microsoft 中的 Lucene 和自然语言处理技术利用语言分析器。
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "72793595"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中为多种语言创建索引

索引可以包括包含多种语言内容的字段，例如，为特定于语言的字符串创建单独的字段。 为了在索引和查询过程中获得最佳结果，请分配提供适当语言规则的语言分析器。 

Azure 认知搜索提供了大量来自 Lucene 和 Microsoft 的语言分析器，可以使用 Analyzer 属性将它们分配给各个字段。 还可以在门户中指定语言分析器，如本文所述。

## <a name="add-analyzers-to-fields"></a>将分析器添加到字段

创建字段时指定语言分析器。 将分析器添加到现有字段定义需要覆盖（并重新加载）索引，或创建与原始字段相同但具有分析器分配的新字段。 然后，可以在方便时删除未使用的字段。

1. 登录 [Azure 门户](https://portal.azure.com)并查找搜索服务。
1. 在服务仪表板顶部的命令栏中单击“添加索引”  即可启动新的索引，或打开现有索引，在添加至现有索引的新字段上设置分析器。
1. 通过提供名称开始字段定义。
1. 选择 Edm.String 数据类型。 只有字符串字段是全文可搜索的。
1. 设置**可搜索**特性以启用 Analyzer 属性。 字段必须基于文本才能使用语言分析器。
1. 选择一个可用的分析器。 

![在字段定义期间分配语言分析器](media/search-language-support/select-analyzer.png "在字段定义期间分配语言分析器")

默认情况下，所有可搜索字段都使用与语言无关的[标准 Lucene 分析器](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)。 若要查看支持的分析器的完整列表，请参阅[将语言分析器添加到 Azure 认知搜索索引](index-add-language-analyzers.md)。

在门户中，分析器旨在按原样使用。 如果需要筛选器和 tokenizer 的自定义设置或特定配置，则应在代码中[创建自定义分析器](index-add-custom-analyzers.md)。 门户不支持选择或配置自定义分析器。

## <a name="query-language-specific-fields"></a>查询语言特定的字段

为字段选择语言分析器后，它用于该字段的每个索引和搜索请求。 当针对使用不同分析器的多个字段发出查询时，查询将由为每个字段分配的分析器独立处理。

如果已知发出查询的代理的语言，可使用 **searchFields** 查询参数，将搜索请求的范围限制为特定字段。 以下查询将仅针对波兰文描述发出：

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

使用[**搜索资源管理器**](search-explorer.md)粘贴类似上述内容的查询，可以从门户查询索引。

## <a name="boost-language-specific-fields"></a>提升语言特定的字段

有时，发出查询的代理的语言未知，在此情况下，可以针对所有字段同时发出查询。 如果需要，可以使用[计分配置文件](index-add-scoring-profiles.md)来定义采用特定语言的结果首选项。 在下面的示例中，与波兰文和法文的匹配项相比，英文描述中提供的匹配项的评分更高：

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>后续步骤

如果是一名 .NET 开发人员，请注意，可以使用 [Azure 认知搜索 .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) 和 [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 属性来配置语言分析器。 