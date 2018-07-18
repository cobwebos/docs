---
title: 必应自定义搜索：获取自定义自动建议 | Microsoft Docs
description: 介绍如何检索自定义自动建议
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365887"
---
# <a name="get-custom-suggestions"></a>获取自定义建议
在向必应自定义搜索发送查询之前，请调用自定义自动建议 API 以提出搜索词建议并增强搜索体验。 自定义自动建议 API 根据用户提供的部分查询字符串返回建议查询的列表。 指定的任何相关自定义查询词都会显示在自动建议生成的建议之前。 有关详细信息，请参阅[定义自定义搜索建议](define-custom-suggestions.md)。

## <a name="endpoint"></a>终结点
若要使用必应自定义搜索 API 获取建议的查询，请向以下终结点发送 `GET` 请求。

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>响应 JSON
该响应列出了包含建议查询词的 SearchAction 对象。

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

每个建议都包含 `displayText` 和 `query` 字段。 `displayText` 字段包含用于填充搜索框下拉列表的建议查询。

如果用户从下拉列表中选择建议的查询，请在调用[必应自定义搜索 API](overview.md) 时在 `query` 字段中使用该查询词。

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>后续步骤

- [调用自定义搜索](./search-your-custom-view.md)
- [配置托管 UI 体验](./hosted-ui.md)