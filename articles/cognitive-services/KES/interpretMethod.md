---
title: 知识探索服务 API 中的 Interpret 方法 | Microsoft Docs
description: 了解如何在认知服务中使用知识探索服务 (KES) API 中的 Interpret 方法。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: ef68d98dacf393abf8d030b9312217ea380947d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365505"
---
# <a name="interpret-method"></a>interpret 方法
interpret 方法采用自然语言查询字符串，并根据语法和索引数据返回对用户意向的格式化解释。  为了提供交互式搜索体验，可以调用此方法，因为每个字符都由用户输入，其中 complete 参数设置为 1 以启用自动完成建议。

## <a name="request"></a>请求
`http://<host>/interpret?query=<query>[&<options>]`

名称|值| 说明
----|----|----
query    | 文本字符串 | 用户输入的查询。  如果 complete 设置为 1，查询将被解释为生成查询自动完成建议的前缀。        
complete | 0（默认值）或 1 | 1 意味着自动完成建议是基于语法和索引数据生成的。         
计数    | 数字（默认值为 10） | 返回的最大解释数。         
offset   | 数字（默认值为 0） | 返回的第一个解释的索引。  例如，count=2&offset=0 返回解释 0 和 1。 count=2&offset=2 返回解释 2 和 3。       
timeout  | 数字（默认值为 1000） | 超时（以毫秒为单位）。 仅返回在超时之前找到的解释。

使用 count 和 offset 参数，可以在多个请求中以增量方式获取大量结果。

## <a name="response-json"></a>响应 (JSON)
JSONPath     | 说明
---------|---------
$.query |query 参数来自请求。
$.interpretations   |将输入查询与语法相匹配的 0 种或多种方法数组。
$.interpretations[\*].logprob   |解释的相对对数概率 (<=0)。  较高值更有可能。
$.interpretations[\*].parse |XML 字符串，显示如何解释查询的各个部分。
$.interpretations[\*].rules |在解释期间调用的语法中定义的 1 个或多个规则数组。
$.interpretations[\*].rules[\*].name    |规则名称。
$.interpretations[\*].rules[\*].output  |规则的语义输出。
$.interpretations[\*].rules[\*].output.type |语义输出的数据类型。
$.interpretations[\*].rules[\*].output.value|语义输出的值。  
$.aborted | 如果请求超时，则为 True。

### <a name="parse-xml"></a>分析 XML
分析 XML 注释（已完成）查询，提供关于如何与索引中的语法和属性中的规则相匹配的信息。  下面是学术出版物领域的一个示例：

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

`<rule>` 元素划定与其 `name` 属性指定的规则相匹配的查询范围。  当解析涉及到语法中的规则引用时，可能会嵌套。

`<attr>` 元素划定与其 `name` 属性指定的索引属性相匹配的查询范围。  当匹配在输入查询中包含同义词时，`canonical` 属性将包含与索引中的同义词匹配的规范值。

## <a name="example"></a>示例
在学术出版物示例中，下面的请求针对前缀查询“papers by jaime”返回最多 2 个自动完成建议：

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

响应包含前两个（“count=2”）最有可能的解释，这两个解释完成了部分“papers by jaime”查询：“papers by jaime teevan”和“papers by jaime green”。  该服务生成查询完成，而不是只考虑作者“jaime”的精确匹配，因为请求指定“complete=1”。 请注意，规范值“j l green”通过同义词“jamie green”匹配，如解析中所示。


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

当语义输出类型为“query”，就像本例所示，可以通过 expr 参数将 output.value 传递到 [evaluate](evaluateMethod.md) API 来检索匹配对象。

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
