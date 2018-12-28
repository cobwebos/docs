---
title: 命名实体识别认知搜索技能 - Azure 搜索
description: 从 Azure 搜索认知搜索管道中的文本中提取人员、位置和组织的命名实体。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e63354152f8821c5ce975563639c8b87fb332bd4
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313983"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>命名实体识别认知技能

命名实体识别技能可以从文本中提取命名实体。 可用实体包括 `person`、`location` 和 `organization` 类型。

> [!NOTE]
> <ul>
> <li>
> 自 2018 年 12 月 21 日起，你可将认知服务资源与 Azure 搜索技能集进行关联。 这将使我们能够开始收取技能集执行的费用。 在此日期，我们还会开始将图像提取视为文档破解阶段的一部分进行计费。 我们将继续提供文档文本提取服务（不收取额外费用）。>
> 内置技能的执行将按现有的[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)进行计费。 图像提取费用将按预览版定价进行计费，详见 [Azure 搜索定价页面](https://go.microsoft.com/fwlink/?linkid=2042400)。 了解[详细信息](cognitive-search-attach-cognitive-services.md)。</li>
> <li> 命名实体识别技能被视为“已弃用”，自 2019 年 2 月 15 日起将不再受到正式支持。 按照<a href="cognitive-search-skill-deprecated.md">已弃用的认知搜索技能</a>页中列出的建议，迁移到支持的技能</li>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 `String.Length` 进行测量。 如果在将数据发送到关键短语提取器之前需要拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 说明 |
|--------------------|-------------|
| categories    | 应提取的类别的数组。  可能类别类型有：`"Person"`、`"Location"`、`"Organization"`。 如果不提供类别，则返回所有类型。|
|defaultLanguageCode |  输入文本的语言代码。 支持以下语言：`de, en, es, fr, it`|
| minimumPrecision  | 介于 0 和 1 之间的数字。 如果精度低于此值，则不会返回该实体。 默认值为 0。|

## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| languageCode  | 可选。 默认为 `"en"`。  |
| text          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

| 输出名称     | 说明                   |
|---------------|-------------------------------|
| 人员      | 一个字符串数组，其中，一个字符串表示一个人员名称。 |
| 位置  | 一个字符串数组，其中，一个字符串表示一个位置。 |
| 组织  | 一个字符串数组，其中，一个字符串表示一个组织。 |
| 实体 | 一个复杂类型数组。 每个复杂类型都包含以下字段： <ul><li>类别（`"person"`、`"organization"` 或 `"location"`）</li> <li>值（实际实体名称）</li><li>偏移（在文本中找到它的位置）</li><li>置信度（一个介于 0 和 1 之间的值，表示值是实际实体的置信度）</li></ul> |

##  <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
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
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>错误案例
如果文档的语言代码不受支持，则返回错误，并且不提取任何实体。

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [实体识别技能](cognitive-search-skill-entity-recognition.md)
