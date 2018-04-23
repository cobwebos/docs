---
title: Azure 搜索中的语言筛选器 | Microsoft Docs
description: 按用户安全标识、语言、地理位置或数字值进行条件筛选可以减少 Azure 搜索（Microsoft Azure 上的托管云搜索服务）中的查询返回的搜索结果。
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.workload: search
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 45d5fb127a4c9df4cf4875ecafbc4ad5ce244998
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-filter-by-language-in-azure-search"></a>如何在 Azure 搜索中按语言筛选 

多语言搜索应用程序的一项关键要求是能够按用户自己的语言搜索和检索结果。 在 Azure 搜索中，满足多语言应用的语言要求的方法之一是创建专门用于按特定语言存储字符串的一系列字段，然后在查询时将全文搜索限定于这些字段。

请求中的查询参数用于限定搜索操作的范围，同时修剪无法提供与所需搜索体验兼容的内容的任何字段的结果。

| parameters | 目的 |
|-----------|--------------|
| **searchFields** | 将全文搜索限制为命名字段的列表。 |
| **$select** | 修剪响应，以便只包含指定的字段。 默认情况下，会返回所有可检索字段。 使用 **$Select** 参数可以选择要返回哪些字段。 |

此方法能够成功取决于字段内容的完整性。 Azure 搜索不会转换字符串，也不执行语言检测。 你负责确保字段包含预期的字符串。

## <a name="define-fields-for-content-in-different-languages"></a>为采用不同语言的内容定义字段

在 Azure 搜索中，查询以单个索引为目标。 想要在单个搜索体验中提供特定于语言的字符串的开发人员通常会定义专用字段来存储值：一个字段用于存储英语字符串，一个字段用于存储法语字符串，等等。 

在我们的示例（包括下面所示的[房地产示例](search-get-started-portal.md)）中，可以看到类似于以下屏幕截图的字段定义。 请注意此示例如何此索引中显示字段的语言分析器分配。 如果与旨在处理目标语言的语言规则的分析器搭配使用，包含字符串的字段可在全文搜索中更好地发挥作用。

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> 有关通过语言分析器显示字段定义的代码示例，请参阅[定义索引 (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) 和[定义索引 (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json)。

## <a name="build-and-load-an-index"></a>生成和加载索引

编写查询之前的一个中间步骤（也许是众所周知的步骤）是[生成并填充索引](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index)。 为了保持内容完整，此处阐述了此步骤。 确定索引是否可用的一种方法是在[门户](https://portal.azure.com)中查看索引列表。

## <a name="constrain-the-query-and-trim-results"></a>约束查询和修剪结果

查询中的参数用于将搜索范围限制为特定的字段，然后修剪对方案无用的任何字段的结果。 假设目标是将搜索范围限定于包含法语字符串的字段，则可以使用 **searchFields** 将查询目标指定为包含法语字符串的字段。 

默认情况下，搜索会返回标记为可检索的所有字段。 因此，可能需要排除不符合想要提供的特定于语言的搜索体验的字段。 具体而言，如果将搜索范围限制为包含法语字符串的字段，也许需要从结果中排除包含英语字符串的字段。 使用 **$select** 查询参数可以控制要将哪些字段返回到调用应用程序。

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> 尽管查询不包含 $filter 自变量，但此用例与筛选概念密切相关，因此我们将它作为筛选方案进行演示。

## <a name="see-also"></a>另请参阅

+ [Azure 搜索中的筛选器](search-filters.md)
+ [语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Azure 搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

