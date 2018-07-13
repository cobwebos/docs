---
title: 学术知识 API 中的 CalcHistogram 方法 | Microsoft Docs
description: CalcHistogram 方法可用于计算 Microsoft 认知服务中一组论文实体的属性值分布。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e0b773fb9791ee638c8cfdbbc9dca40543e50ec0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365409"
---
# <a name="calchistogram-method"></a>CalcHistogram 方法

calchistogram REST API 用于计算一组论文实体的属性值分布。          


**REST 终结点：**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>请求参数

名称  |值 | 必需？  |说明
-----------|----------|--------|----------
**expr**    |文本字符串 | 是  |指定计算哪些实体的直方图的查询表达式。
**model** |文本字符串 | 否 |选择要查询的模型的名称。  默认值暂为“latest”。
**attributes** | 文本字符串 | 否<br>默认值： | 指定响应中包含的属性值的逗号分隔列表。 属性名区分大小写。
**count** |Number | 否<br>默认值：10 |要返回的结果数。
**offset**  |Number | 否<br>默认值：0 |要返回的第一个结果的索引。
<br>
## <a name="response-json"></a>响应 (JSON)
名称 | 说明
--------|---------
**expr**  |请求中的 expr 参数。
**num_entities** | 匹配实体总数。
**histograms** |  一组直方图，请求中指定的每个属性都对应有一个直方图。
**histograms[x].attribute** | 对其计算直方图的属性的属性名。
**histograms[x].distinct_values** | 与此属性匹配的实体中的非重复值数。
**histograms[x].total_count** | 与此属性匹配的实体中的值实例总数。
**histograms[x].histogram** | 此属性的直方图数据。
**histograms[x].histogram[y].value** |  属性值。
**histograms[x].histogram[y].logprob**  |与此属性值匹配的实体的自然对数总概率。
**histograms[x].histogram[y].count**  |与此属性值匹配的实体数。
**aborted** | 如果请求超时，则为 True。

 <br>
#### <a name="example"></a>示例：
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>在此示例中，为了生成自 2010 年起某作者的出版物年计数直方图，可以先结合使用 interpret API 和下面的查询字符串来生成查询表达式：papers by jaime teevan after 2012。

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>interpret API 返回的首个解释中的表达式是 And(Composite(AA.AuN=='jaime teevan'),Y>2012)。
<br>然后，此表达式值会传递给 calchistogram API。 attributes=Y,F.FN 参数指明，论文计数分布应按“年份”和“研究领域”划分，例如：
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>此请求的响应首先指明，有 37 篇论文与查询表达式匹配。  对于“年份”属性，有 3 个非重复值，2012 年后每年一个（即 2013 年、2014 年和 2015 年），如查询中所指定。  3 个非重复值的论文总计数为 37。  对于每个“年份”，直方图都显示值、自然对数总概率和匹配实体数。     

“研究领域”直方图显示，有 34 个不同的研究领域。 由于一篇论文可能与多个研究领域相关联，因此总计数 (53) 可能大于匹配实体数。  虽然有 34 个非重复值，但由于 count=4 参数，响应只包含前 4 个值。

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
