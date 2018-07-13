---
title: 学术知识 API 中的 Evaluate 方法 | Microsoft Docs
description: Microsoft 认知服务中的 Evaluate 方法用于根据查询表达式返回一组学术实体。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 3005ae1f6df042a49db086de4982d8206f6938a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365395"
---
# <a name="evaluate-method"></a>Evaluate 方法

evaluate REST API 用于根据查询表达式返回一组学术实体。
<br>

**REST 终结点：**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>
## <a name="request-parameters"></a>请求参数  
名称     | 值 | 必需？  | 说明
-----------|-----------|---------|--------
**expr**       | 文本字符串 | 是 | 指定应返回哪些实体的查询表达式。
**model**      | 文本字符串 | 否  | 要查询的模型的名称。  默认值暂为“latest”。        
**attributes** | 文本字符串 | 否<br>默认值：Id | 指定响应中包含的属性值的逗号分隔列表。 属性名区分大小写。
**count**        | Number | 否<br>默认值：10 | 要返回的结果数。
**offset**     | Number |   否<br>默认值：0    | 要返回的第一个结果的索引。
**orderby** |   文本字符串 | 否<br>默认值：by decreasing prob | 用于对实体排序的属性的属性名。 可以视需要指定升序/降序。 格式为 name:asc 或 name:desc。
  
 <br>
## <a name="response-json"></a>响应 (JSON)
名称 | 说明
-------|-----   
**expr** |  请求中的 expr 参数。
**entities** |  一组与查询表达式匹配的 0 个或多个实体。 每个实体都包含自然对数概率值，以及其他请求获取的属性值。
**aborted** | 如果请求超时，则为 True。

<br>
#### <a name="example"></a>示例：
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>表达式通常是通过响应 interpret 方法获得。  不过，也可以自行编写查询表达式（请参阅[查询表达式语法](QueryExpressionSyntax.md)）。  
  
使用 count 和 offset 参数，无需发送单个可能会生成巨大响应（且可能会导致速度变慢）的请求，即可获得大量结果。  在此示例中，请求将 interpret API 响应返回的首个解释中的表达式用作 expr 值。 count=2 参数指定，要请求获取 2 个实体结果。 attributes=Ti,Y,CC,AA.AuN,AA.AuId 参数指明，请求在每个结果中获取标题、年份、引文计数、作者姓名和作者 ID。  有关属性列表，请参阅[实体属性](EntityAttributes.md)。
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
