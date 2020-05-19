---
title: 转换器字典查找方法
titleSuffix: Azure Cognitive Services
description: 字典查找方法可为某个单词和少量的惯用语提供替代翻译。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b2d111f22b8ef36b20b93b65ff1ea6f7b52ea8f7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584733"
---
# <a name="translator-30-dictionary-lookup"></a>转换器3.0：字典查找

为某个单词和少量的惯用语提供替代翻译。 每条翻译都包含语性和回译列表。 回译可让用户在语境中理解该翻译。 使用[字典示例](./v3-0-dictionary-examples.md)操作可以进一步向下钻取，以查看每个翻译对的用法示例。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

| 查询参数  | 说明 |
| ------ | ----------- |
| api-version <img width=200/>   | 必需参数  。<br/>客户端所请求的 API 的版本。 值必须是 `3.0` |
| 从 | 必需参数  。<br/>指定输入文本的语言。 源语言必须是 [ 范围中包含的](./v3-0-languages.md)支持的语言`dictionary`之一。 |
| to   | 必需参数  。<br/>指定输出文本的语言。 目标语言必须是 [ 范围中包含的](v3-0-languages.md)支持的语言`dictionary`之一。 |


请求标头包括：

| 标头  | 说明 |
| ------ | ----------- |
| 身份验证标头 <img width=200/>  | 必需的请求标头  。<br/>请参阅<a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">用于身份验证的可用选项</a>。 |
| Content-Type | 必需的请求标头  。<br/>指定有效负载的内容类型。 可能的值为：`application/json` |
| Content-Length   | 必需的请求标头  。<br/>请求正文的长度。 |
| X-ClientTraceId   | **可选**。<br/>客户端生成的 GUID，用于唯一标识请求。 如果在查询字符串中使用名为 `ClientTraceId` 的查询参数包括了跟踪 ID，则可以省略此标头。 |

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个 JSON 对象，具有一个名为 `Text` 的字符串属性，该属性表示要查找的字词。

```json
[
    {"Text":"fly"}
]
```

以下限制适用：

* 该数组最多可具有 10 个元素。
* 数组元素的文本值不能超过 100 个字符（包括空格）。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个字符串。 结果对象包括以下属性：

  * `normalizedSource`：一个字符串，提供源字词的规范化形式。 例如，如果请求为“JOHN”，则规范化形式为“john”。 此字段的内容将成为[查找示例](./v3-0-dictionary-examples.md)的输入。
    
  * `displaySource`：一个字符串，以最适合向最终用户显示的形式提供源字词。 例如，如果输入为“JOHN”，则显示形式将反映该名字的一般拼写方式：“John”。 

  * `translations`：源字词的翻译列表。 每个列表元素都是一个具有以下属性的对象：

    * `normalizedTarget`：一个字符串，以目标语言提供此字词的规范化形式。 此值应用作[查找示例](./v3-0-dictionary-examples.md)的输入。

    * `displayTarget`：一个字符串，使用目标语言以最适合向最终用户显示的形式提供字词。 一般情况下，此值只是在大小写方面与 `normalizedTarget` 不同。 例如，专有名词“Juan”的拼写方式包括 `normalizedTarget = "juan"` 和 `displayTarget = "Juan"`。

    * `posTag`：一个字符串，用于将此字词与词性标记相关联。

        | 标记名称 | 说明  |
        |----------|--------------|
        | ADJ      | 形容词   |
        | ADV      | 副词      |
        | CONJ     | 连词 |
        | DET      | 限定词  |
        | MODAL    | 动词        |
        | NOUN     | 名词        |
        | PREP     | 介词 |
        | PRON     | 代词     |
        | VERB     | 动词        |
        | OTHER    | 其他        |

        实现说明：这些标记是由标记英语字词的词性确定的，采用每个源/目标对的最常用标记。 因此，如果用户经常将某个西班牙语单词翻译成英语中的不同词性标记，则标记最终可能会出错（相对于西班牙语单词）。

    * `confidence`：介于 0.0 和 1.0 之间的值，表示该翻译对的“置信度”（更准确地说，可能表示“训练数据中的概率”）。 一个源单词的置信度评分之和不一定等于 1.0。 

    * `prefixWord`：一个字符串，提供要显示为翻译前缀的单词。 目前，在限定词区分性别的语言中，此值是指名词的性别限定词。 例如，西班牙语单词“mosca”的前缀是“la”，因为在西班牙语中，“mosca”是阴性名词。 此标记只与翻译结果相关，而与源无关。 如果没有前缀，则它是空字符串。
    
    * `backTranslations`：目标的“回译”列表。 例如，目标可以翻译成的源单词。 保证该列表包含请求的源单词（例如，如果查找的源单词是“fly”，则保证“fly”在 `backTranslations` 列表中）。 但是，不能保证该单词位于第一个位置，并且它往往不是在第一个位置。 `backTranslations` 列表的每个元素是以下属性描述的对象：

        * `normalizedText`：一个字符串，提供目标回译成的源单词的规范化形式。 此值应用作[查找示例](./v3-0-dictionary-examples.md)的输入。        

        * `displayText`：一个字符串，以最适合向最终用户显示的形式，提供目标回译成的源单词。

        * `numExamples`：一个整数，表示此翻译对可用的示例数。 必须通过单独调用[查找示例](./v3-0-dictionary-examples.md)来检索实际示例。 提供该数字的主要目的是方便在 UX 中显示。 例如，如果示例数大于零，则用户界面可以添加回译的超链接；如果没有任何示例，则以纯文本形式显示回译。 请注意，调用[查找示例](./v3-0-dictionary-examples.md)后返回的实际示例数可能小于 `numExamples`，因为可能对“fly”应用了其他筛选，以删除“错误的”示例。
        
        * `frequencyCount`：一个整数，表示此翻译对在数据中出现的频率。 此字段的主要用途是在用户界面中提供一种方式用于将回译排序，使最常见的字词出现在最前面。

    > [!NOTE]
    > 如果查找的字词在字典中不存在，则响应为 200 (OK)，但 `translations` 列表为空。

## <a name="examples"></a>示例

此示例演示如何在西班牙语中查找英语字词 `fly` 的替代翻译。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

响应正文是（为清楚起见已缩写）：

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

此示例演示当查找的字词在有效的字典对中不存在时会发生什么情况。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

由于在字典中未找到该字词，响应正文包含一个空 `translations` 列表。

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
