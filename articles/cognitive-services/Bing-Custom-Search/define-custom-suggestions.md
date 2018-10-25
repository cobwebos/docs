---
title: 定义自定义自动建议的建议 - 必应自定义搜索
titlesuffix: Azure Cognitive Services
description: 介绍如何为自定义自动建议配置自定义建议
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 6d7ee4ef1a84b644a7075e24c0760ea46b407754
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465536"
---
# <a name="configure-your-custom-autosuggest-experience"></a>配置自定义自动建议体验

自定义自动建议可返回与搜索体验相关的建议搜索查询字符串列表。 建议的查询字符串基于用户在搜索框中提供的部分查询字符串。 该列表中包含最多 10 条建议。 

指定只返回自定义建议还是包括必应建议。 如果包括必应建议，则自定义建议在必应建议之前显示。 如果提供相关度充足的建议，返回的建议列表中可能不包含必应建议。 必应建议始终在自定义搜索实例的上下文中。 

若要为实例配置搜索查询建议，请单击“自动建议”选项卡。  

> [!NOTE]
> 若要使用此功能，必须订阅相应级别的自定义搜索（请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)）。

可能需要 24 小时才会在服务终结点（API 或托管 UI）中反映建议。

## <a name="enable-bing-suggestions"></a>启用必应建议

若要启用必应建议，请将“自动必应建议”滑块切换到开启位置。 滑块变为蓝色。

## <a name="add-your-own-suggestions"></a>添加自己的建议

若要添加自己的查询字符串建议，请将它们添加到“用户定义的建议”下的列表中。 在列表中添加建议后，按 Enter 键或单击“+”图标。 可采用任何语言来指定建议。 可以添加最多 5,000 条查询字符串建议。

## <a name="upload-suggestions"></a>上传建议

可选择从文件上传建议列表。 该文件必须每行一个搜索查询字符串。 要上传文件，请单击上传图标，然后选择要上传的文件。 该服务从文件中提取建议，并将其添加到列表。

## <a name="remove-suggestions"></a>删除建议

若要删除查询字符串建议，请单击要删除的建议旁边的删除图标。

## <a name="block-suggestions"></a>阻止建议

如果包括了必应建议，可以添加不想必应返回的搜索查询字符串列表。 要添加阻止的查询字符串，请单击“显示阻止的建议”。 向列表中添加查询字符串后，按 Enter 键或单击“+”图标。 可以添加最多 50 条阻止的查询字符串。



[!INCLUDE[publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>自定义自动建议配置更改最多可能需要 24 小时才能生效。


## <a name="enabling-autosuggest-in-hosted-ui"></a>在托管 UI 中启用自动建议

要为托管 UI 启用查询字符串建议，请单击“托管 UI”。 向下滚动到“其他配置”部分。 在“Web 搜索”下，为“启用自动建议”选择“开”。 要启用自动建议，必须选择包含搜索框的布局。


## <a name="calling-the-autosuggest-api"></a>调用自动建议 API

若要使用必应自定义搜索 API 获取建议的查询字符串，请向以下终结点发送 `GET` 请求。

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

该响应列出了包含建议查询字符串的 `SearchAction` 对象。

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

每个建议都包含 `displayText` 和 `query` 字段。 `displayText` 字段包含用于填充搜索框下拉列表的建议查询字符串。

如果用户从下拉列表中选择建议的查询字符串，请在调用[必应自定义搜索 API](overview.md) 时在 `query` 字段中使用该查询字符串。


## <a name="next-steps"></a>后续步骤

- [获取自定义建议](./get-custom-suggestions.md)
- [搜索自定义实例](./search-your-custom-view.md)
- [配置和使用自定义托管 UI](./hosted-ui.md)