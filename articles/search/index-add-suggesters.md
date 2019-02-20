---
title: 向 Azure 搜索索引添加建议器
description: 允许字段执行提前键入的查询操作，其中的建议查询由 Azure 搜索索引中各字段的文本组成。
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 35025d69865aa6890e1cd921e31ac6c26c015789
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007695"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>向 Azure 搜索索引添加建议器

**建议器**是一项 Azure 搜索构造，支持“按键入搜索”[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)功能和[自动完成（预览版）](search-autocomplete-tutorial.md)功能。 在调用建议 API 之前，必须在索引中定义**建议器**，以便在特定字段上启用建议。

虽然**建议器**有多种属性，但它主要是你要为其启用[建议 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 的字段的集合。 例如，旅行应用可能需要针对目的地、城市和旅游胜地启用提前键入。 因此，所有三个字段都会进入字段集合中。

每个索引只能有一个**建议器**资源（具体说来，在**建议器**集合中只能有一个**建议器**）。

可以随时创建**建议器**，但对索引的影响因字段而异。 在同一更新过程中添加到建议器的新字段是影响力最小的，因为不需重新生成索引。 但是，添加现有字段会更改字段定义，因此必须完完全全地重新生成索引。

## <a name="usage"></a>使用情况  

 **建议器**最适用于建议特定的文档（而不是松散的字词或短语）。 最佳的候选字段是标题、名称和其他可标识某个项目的相对较短的短语。 效果较差的是重复性字段（如类别和标记）或非常长的字段（如说明或评论字段）。  

在创建建议器以后，请在查询逻辑中添加[建议 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)，以便调用该功能。  

定义**建议器**的属性包括以下内容：  

|属性|说明|  
|--------------|-----------------|  
|`name`|**建议器**的名称。 请在调用[建议（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/suggestions)时使用**建议器**的名称。|  
|`searchMode`|用于搜索候选短语的策略。 当前唯一受支持的模式是 `analyzingInfixMatching`，此模式在句子开头或中间执行短语的灵活匹配。|  
|`sourceFields`|作为建议内容源的一个或多个字段的列表。 只有 `Edm.String` 和 `Collection(Edm.String)` 类型的字段可作为建议的源。 只能使用没有自定义语言分析器的字段。 |  

## <a name="suggester-example"></a>建议器示例  
 **建议器**是索引定义的一部分。 在当前版本的 **fields** 集合和 **scoringProfiles** 旁边，**suggesters** 集合中只能存在一个**建议器**。  

```  
{  
  "name": "hotels",  
  "fields": [  
     . . .   
   ],  
  "suggesters": [  
    {  
    "name": "sg",  
    "searchMode": "analyzingInfixMatching",  
    "sourceFields": ["hotelName", "category"]  
    }  
  ],  
  "scoringProfiles": [  
     . . .   
  ]  
}  

```  

## <a name="see-also"></a>另请参阅  
 [创建索引（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [更新索引（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/update-index)   
 [建议（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/suggestions)   
 [索引操作（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/index-operations)   
 [Azure 搜索服务 REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Azure 搜索 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
