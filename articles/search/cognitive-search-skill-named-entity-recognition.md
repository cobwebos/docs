---
title: 命名实体识别认知搜索技能（Azure 搜索）| Microsoft Docs
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
ms.openlocfilehash: f9ff3f66f3a73fbaf1a4c2ca280c85f4bde65444
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442023"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>命名实体识别认知技能

命名实体识别技能可以从文本中提取命名实体。 可用实体包括 `person`、`location` 和 `organization` 类型。

> [!NOTE]
> <ul>
> <li>Azure 搜索目前提供公共预览版。 技能集执行以及图像的提取和规范化目前免费提供。 我们日后会公布这些功能的定价。 </li>
> <li> 命名实体识别技能被视为“已弃用”，自 2019 年 2 月 15 日起将不再受到正式支持。 按照<a href="cognitive-search-skill-deprecated.md">弃用的认知搜索技能</a>页面中列出的建议，迁移到支持的技能</li>

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
如果不支持文档的语言代码，则返回错误，并且不提取任何实体。

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [实体识别技能](cognitive-search-skill-entity-recognition.md)
