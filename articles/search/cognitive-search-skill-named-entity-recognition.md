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
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: eeac83bb0bb4ad873873a99721c9269659f2a685
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841033"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>命名实体识别认知技能

命名实体识别技能可以从文本中提取命名实体。 可用实体包括 `person`、`location` 和 `organization` 类型。

> [!IMPORTANT]
> 已命名的实体识别技能现已弃用，替换为 [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)。 已于 2019 年 2 月 15 日停止支持，并且已于 2019 年 5 月 2 日将此 API 从产品中删除。 建议按照[已弃用的认知搜索技能](cognitive-search-skill-deprecated.md)来迁移到受支持的技能。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API，以及在 Azure 搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为50000个字符[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果在将数据发送到关键短语提取器之前需要拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

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
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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

## <a name="see-also"></a>请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [实体识别技能](cognitive-search-skill-entity-recognition.md)
