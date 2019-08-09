---
title: 用于非英语搜索查询的多语言索引 - Azure 搜索
description: Azure 搜索支持 56 种语言，通过 Microsoft 中的 Lucene 和自然语言处理技术利用语言分析器。
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: jlembicz
ms.openlocfilehash: 5383ad44f665ce809772143e23817932c3e2b7e6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883889"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-search"></a>如何在 Azure 搜索中为多种语言创建索引

索引可以包括包含多种语言内容的字段, 例如, 为特定于语言的字符串创建单个字段。 若要在建立索引和查询时获得最佳结果, 请分配提供适当语言规则的语言分析器。 

Azure 搜索提供了来自 Lucene 和 Microsoft 的大量所选语言分析器, 可使用 Analyzer 属性分配给各个字段。 你还可以在门户中指定语言分析器, 如本文所述。

## <a name="add-analyzers-to-fields"></a>向字段添加分析器

语言分析器在创建字段时指定。 向现有字段定义添加分析器需要覆盖 (和重新加载) 索引, 或者创建一个与原始字段相同的新字段, 但使用分析器赋值。 然后, 您可以在方便时删除未使用的字段。

1. 登录到[Azure 门户](https://portal.azure.com)并找到搜索服务。
1. 在服务仪表板顶部的命令栏中单击“添加索引”即可启动新的索引，或打开现有索引，在添加至现有索引的新字段上设置分析器。
1. 通过提供名称来启动字段定义。
1. 选择 "Edm" 数据类型。 只有字符串字段可以进行全文搜索。
1. 设置可**搜索**属性以启用 Analyzer 属性。 字段必须基于文本, 才能使用语言分析器。
1. 选择一个可用分析器。 

![在字段定义期间分配语言分析器](media/search-language-support/select-analyzer.png "在字段定义期间分配语言分析器")

默认情况下, 所有可搜索字段均使用与语言无关的[标准 Lucene 分析器](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)。 若要查看受支持分析器的完整列表, 请参阅[将语言分析器添加到 Azure 搜索索引](index-add-language-analyzers.md)。

在门户中, 分析器旨在按原样使用。 如果需要自定义或筛选器和 tokenizer 的特定配置, 应在代码中[创建自定义分析器](index-add-custom-analyzers.md)。 门户不支持选择或配置自定义分析器。

## <a name="query-language-specific-fields"></a>查询特定于语言的字段

为字段选择语言分析器后，它用于该字段的每个索引和搜索请求。 使用不同分析器对多个字段发出查询时, 查询将由每个字段的已分配分析器单独处理。

如果已知发出查询的代理的语言，可使用 **searchFields** 查询参数，将搜索请求的范围限制为特定字段。 以下查询将仅针对波兰文描述发出：

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

可以从门户查询索引, 使用 "[**搜索资源管理器**](search-explorer.md)" 粘贴类似于上面所示的查询。

## <a name="boost-language-specific-fields"></a>提升特定于语言的字段

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

如果你是 .NET 开发人员, 请注意, 可以使用[Azure 搜索 .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search)和[Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)属性配置语言分析器。 