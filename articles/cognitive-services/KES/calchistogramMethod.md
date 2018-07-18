---
title: 知识探索服务 API 中的 CalcHistogram 方法 | Microsoft Docs
description: 了解如何在认知服务中使用知识探索服务 (KES) API 中的 CalcHistogram 方法。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 6ed694b0cc9cf41b815cc54b9f6d12adb2b7cd64
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365475"
---
# <a name="calchistogram-method"></a>calchistogram 方法
calchistogram 方法可计算与结构化查询表达式匹配的对象，并计算其属性值的分布。

## <a name="request"></a>请求
`http://<host>/calchistogram?expr=<expr>[&options]` 

名称|值|说明
----|-----|-----------
expr | 文本字符串 | 指定用于计算直方图的索引实体的结构化查询表达式。
attributes | 文本字符串（默认值为 ""） | 要包含在响应中的以逗号分隔的属性列表。
计数   | 数字（默认值为 10） | 要返回的结果数。
offset  | 数字（默认值为 0） | 要返回的第一个结果的索引。

## <a name="response-json"></a>响应 (JSON)
JSONPath | 说明
----|----
$.expr | expr 参数来自请求。
$.num_entities | 匹配实体的总数。
$.histograms |  直方图数组，每个请求的属性一个。
$.histograms[\*].attribute | 计算直方图的属性的名称。
$.histograms[\*].distinct_values | 此属性的匹配实体中的非重复值的数量。
$.histograms[\*].total_count | 此属性的匹配实体中的值实例的总数。
$.histograms[\*].histogram | 此属性的直方图数据。
$.histograms[\*].histogram[\*].value | 属性值。
$.histograms[\*].histogram[\*].logprob  | 具有此属性值的匹配实体的自然对数总概率。
$.histograms[\*].histogram[\*].count    | 具有此属性值的匹配实体的数量。
$.aborted | 如果请求超时，则为 True。

### <a name="example"></a>示例
在学术出版物示例中，以下代码计算自 2013 年以来针对特定作者按年份和按关键字的出版物计数直方图：

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

响应表明有 37 篇论文与查询表达式匹配。  对于 Year 属性，有 3 个非重复值，自 2013 年以来每年一个。  3 个非重复值的论文总计数为 37。  对于每个 Year，直方图显示值、自然对数总概率和匹配实体的数量。     

Keyword 的直方图显示有 34 个非重复关键字。 由于论文可以与多个关键字相关联，因此总计数 (53) 可以大于匹配实体的数量。  虽然有 34 个非重复值，但由于“count=4”参数，响应仅包括前 4 个。

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
