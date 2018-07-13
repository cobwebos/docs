---
title: 语言分析 API 中的分析器方法 | Microsoft Docs
description: 分析器 REST API 提供了 Microsoft 认知服务中服务当前支持的分析器列表。
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365421"
---
# <a name="analyzers-method"></a>分析器方法

分析器 REST API 提供了服务当前支持的分析器列表。
响应包括其[名称](Analyzer-Names.md)以及每个名称支持的语言（例如英语中的“en”）。

## <a name="request-parameters"></a>请求参数
无

<br>

## <a name="response-parameters"></a>响应参数
名称 | Type | 说明
-----|------|--------------
语言 | 字符串列表 | 此分析器可以使用的两个字母 ISO 语言代码的列表。
id   | 字符串 | 此分析器的唯一 ID
kind | 字符串 | 此分析器的广泛类型
specification | 字符串 | 用于此分析器规范的名称
implementation | 字符串 | 此分析器后的模型和/或算法描述

<br>
## <a name="example"></a>示例
GET /analyzers

响应：JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3", 
        "implementation": "SplitMerge"
    }, 
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags", 
        "specification": "PennTreebank3", 
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens", 
        "specification":"PennTreebank3", 
        "implementation": "regexes"
    } 
]
```
