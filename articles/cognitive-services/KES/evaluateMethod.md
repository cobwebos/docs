---
title: 知识探索服务 API 中的 Evaluate 方法 | Microsoft Docs
description: 了解如何使用认知服务内知识探索服务 (KES) API 中的 Evaluate 方法。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365488"
---
# <a name="evaluate-method"></a>evaluate 方法
evaluate 方法根据索引数据，计算并返回结构化查询表达式的输出。

表达式通常是通过响应 interpret 方法获得。  不过，也可以自己编写查询表达式（请参阅[结构化查询表达式](Expressions.md)）。  

## <a name="request"></a>请求 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

名称|值|说明
----|----|----
expr       | 文本字符串 | 选择索引实体子集的结构化查询表达式。
attributes | 文本字符串 | 要在响应中包含的属性的逗号分隔列表。
计数      | 数字（默认值为 10） | 要返回的结果数上限。
offset     | 数字（默认值为 0） | 要返回的第一个结果的索引。
orderby |   文本字符串 | 用于对结果排序的属性的属性名，后跟可选排序顺序（默认值为 asc）：“attrname[:(asc&#124;desc)]”。  如果未指定，返回按自然对数概率降序排列的结果。
timeout  | 数字（默认值为 1000） | 超时（以毫秒为单位）。 仅返回在超时前计算出的结果。

使用 count 和 offset 参数，可以通过多个请求以递增方式获取大量结果。
  
## <a name="response-json"></a>响应 (JSON)
JSONPath|说明
----|----
$.expr | 请求中的 expr 参数。
$.entities | 一组与结构化查询表达式匹配的 0 个或多个对象实体。 
$.aborted | 如果请求超时，则为 True。

每个实体都包含 logprob 值，以及请求获取的属性值。

## <a name="example"></a>示例
在学术出版物示例中，下面的请求传递结构化查询表达式（可能来自 interpret 请求输出），并检索前 2 个匹配实体的几个属性：

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

响应包含前 2 (“count=2”) 个最佳匹配实体。  对于每个实体，都会返回标题、年份、作者姓名和作者 ID 属性。  请注意，复合属性值结构与数据文件中指定的方式一致。 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
