---
title: 实体识别认知搜索技能 - Azure 搜索
description: 从 Azure 搜索认知搜索管道中的文本提取各种类型的实体。
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: eb196b9ff72201358982764f227ab7935489f05d
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183403"
---
#    <a name="entity-recognition-cognitive-skill"></a>实体识别认知技能

**实体识别**技能从文本中提取各种类型的实体。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API，以及在 Azure 搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为50000个字符[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果在将数据发送到关键短语提取器之前需要拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写并且都是可选的。

| 参数名称     | 说明 |
|--------------------|-------------|
| categories    | 应提取的类别的数组。  可能的类别类型有：`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"Datetime"`、`"URL"`、`"Email"`。 如果不提供类别，则返回所有类型。|
|defaultLanguageCode |  输入文本的语言代码。 支持以下语言：`de, en, es, fr, it`|
|minimumPrecision | 未使用。 留待将来使用。 |
|includeTypelessEntities | 当设置为 true 时，如果文本包含某个已知实体，但无法分类为受支持的类别之一，则它将作为 `"entities"` 复杂输出字段的一部分返回。 
这些实体是已知的但未归类到当前受支持的“类别”。 例如，“Windows 10”是一个已知实体（产品），但“Products”不在当前受支持的类别中。 默认为 `false` |


## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| languageCode  | 可选。 默认为 `"en"`。  |
| text          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

> [!NOTE]
> 并非所有实体类别都支持所有语言。 只有 _en_、_es_ 支持 `"Quantity"`、`"Datetime"`、`"URL"`、`"Email"` 类型的提取。

| 输出名称     | 说明                   |
|---------------|-------------------------------|
| 人员      | 一个字符串数组，其中，一个字符串表示一个人员名称。 |
| 位置  | 一个字符串数组，其中，一个字符串表示一个位置。 |
| 组织  | 一个字符串数组，其中，一个字符串表示一个组织。 |
| quantities  | 一个字符串数组，其中，每个字符串都表示一个数量。 |
| dateTimes  | 一个字符串数组，其中，每个字符串都表示一个日期时间（因为它以文本形式显示）值。 |
| urls | 一个字符串数组，其中，每个字符串都表示一个 URL |
| emails | 一个字符串数组，其中，每个字符串都表示一个电子邮件地址 |
| namedEntities | 复杂类型的数组，包含以下字段： <ul><li>category</li> <li>value（实际实体名称）</li><li>偏移（在文本中找到它的位置）</li><li>confidence（现在未使用。 将设置为值为 -1）</li></ul> |
| 实体 | 一个复杂类型数组，包含有关从文本提取的实体的丰富信息，具有以下字段 <ul><li> name（实际实体名称。 这表示一个“规范化”窗体）</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl（实体的 Wikipedia 页面的链接）</li><li>bingId</li><li>type（识别的实体的类别）</li><li>subType（仅适用于某些类别，这提供实体类型的更精细视图）</li><li> matches（包含的复杂集合）<ul><li>text（实体的原始文本）</li><li>offset（找到它的位置）</li><li>length（原始实体文本的长度）</li></ul></li></ul> |

##  <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>示例输入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>示例输出

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>错误案例
如果文档的语言代码不受支持，则返回错误，并且不提取任何实体。

## <a name="see-also"></a>请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
