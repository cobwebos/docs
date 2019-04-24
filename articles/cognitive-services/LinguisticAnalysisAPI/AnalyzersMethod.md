---
title: 分析器方法 - 语言分析 API
titlesuffix: Azure Cognitive Services
description: 分析器 REST API 提供了语言分析 API 当前支持的分析器的列表。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404701"
---
# <a name="analyzers-method"></a>分析器方法

> [!IMPORTANT]
> 语言分析预览版已在 2018 年 8 月 9 日停止使用。 我们建议使用 [Azure 机器学习文本分析模块](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)进行文本处理和分析。

分析器 REST API 提供了服务当前支持的分析器列表。
响应包括其[名称](Analyzer-Names.md)以及每个名称支持的语言（例如英语中的“en”）。

## <a name="request-parameters"></a>请求参数
无

<br>

## <a name="response-parameters"></a>响应参数

Name | 类型 | 描述
-----|------|--------------
语言 | 字符串列表 | 此分析器可以使用的两个字母 ISO 语言代码的列表。
id   | string | 此分析器的唯一 ID
kind | string | 此分析器的广泛类型
specification | string | 用于此分析器规范的名称
implementation | string | 此分析器后的模型和/或算法描述

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
