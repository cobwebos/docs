---
title: 学术知识 API 中的 Interpret 方法 | Microsoft Docs
description: 使用 Interpret 方法根据学术关系图数据和 Microsoft 认知服务中的学术语法返回用户查询字符串的格式化解释。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a46c792f14fabf6562666d1067ef880bd505741f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365417"
---
# <a name="interpret-method"></a>interpret 方法

Interpret REST API 接受最终用户查询字符串（即，由应用程序用户输入的查询），并基于学术关系图数据和学术语法返回用户有意向的格式化解释。

要提供交互式体验，你可以在用户输入的每个字符后重复调用此方法。 在这种情况下，你应将 complete 参数设置为 1，以启用自动完成建议。 如果应用程序不需要自动完成，则应将 complete 参数设置为 0。

REST 终结点：

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>请求参数

名称     | 值 | 必需？  | 说明
---------|---------|---------|---------
**query**    | 文本字符串 | 是 | 用户输入的查询。  如果 complete 设置为 1，查询将被解释为生成查询自动完成建议的前缀。        
**model**    | 文本字符串 | 否  | 想要查询的模型的名称。  当前，值默认为“最新”。        
complete | 0 或 1 | 否<br>default:0  | 1 意味着自动完成建议是基于语法和关系图数据生成的。         
**count**    | Number | 否<br>default:10 | 返回的最大解释数。         
offset   | Number | 否<br>default:0  | 返回的第一个解释的索引。 例如，count=2&offset=0 返回解释 0 和 1。 count=2&offset=2 返回解释 2 和 3。       
timeout  | Number | 否<br>default:1000 | 超时（以毫秒为单位）。 仅返回在超时之前找到的解释。
<br>
  
## <a name="response-json"></a>响应 (JSON)
名称     | 说明
---------|---------
**query** |query 参数来自请求。
interpretations |用于将用户输入与语法进行匹配的 0 种或更多种不同方式的数组。
interpretations[x].logprob  |解释的相对自然对数概率。 较大值更有可能。
interpretations[x].parse  |XML 字符串，显示如何解释查询的各个部分。
interpretations[x].rules  |在解释期间调用的语法中定义的 1 个或多个规则数组。 对于学术知识 API，始终只有 1 条规则。
interpretations[x].rules[y].name  |规则名称。
interpretations[x].rules[y].output  |规则输出。
interpretations[x].rules[y].output.type |规则输出的数据类型。  对于学术知识 API，数据类型始终为“查询”。
interpretations[x].rules[y].output.value  |规则输出。 对于学术知识 API，该输出可以将查询表达式字符串传递给 evaluate 和 calchistogram 方法。
aborted | 如果请求超时，则为 True。

<br>
#### <a name="example"></a>示例：
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>以下响应包含前两个（归因于参数 count=2）最有可能的解释，这两个解释完成了部分 papers by jaime 用户输入：papers by jaime teevan  和 papers by jaime green。  该服务生成查询完成，而不是只考虑作者 jaime 的精确匹配，因为请求指定 complete=1。 请注意，规范值 j l green 通过同义词 jamie green 匹配，如解析中所示。


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>要检索解释的实体结果，请使用解释 API 中的 output.value，并通过 expr 参数将其传递到评估 API。 在此示例中，第一个解释的查询是： 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 