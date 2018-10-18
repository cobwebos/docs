---
title: 关键短语提取认知搜索技能（Azure 搜索）| Microsoft Docs
description: 计算非结构化的文本，并针对每个记录返回 Azure 搜索扩充管道中的关键短语列表。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 694271115626c652523be34160ad6a07053f6387
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735792"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>关键短语提取认知技能

关键短语提取技能可以计算非结构化的文本，并针对每个记录返回关键短语列表。

如果你需要快速确定记录中的谈话要点，此功能十分有用。 例如，给定输入文本“The food was delicious and there were wonderful staff”，服务会返回“food”和“wonderful staff”。

> [!NOTE]
> Azure 搜索目前以公共预览版提供。 技能集执行以及图像的提取和规范化目前免费提供。 我们日后会公布这些功能的定价。 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 `String.Length` 进行测量。 如果在将数据发送到关键短语提取器之前需要拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。
| 输入                | 说明 |
|---------------------|-------------|
| defaultLanguageCode | （可选）要应用到未显式指定语言的文档的语言代码。  如果未指定默认语言代码，会将英语 (en) 用作默认语言代码。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)。 |
| maxKeyPhraseCount   | （可选）要生成的关键短语的最大数量。 |

## <a name="skill-inputs"></a>技能输入
| 输入     | 说明 |
|--------------------|-------------|
| text | 要分析的文本。|
| languageCode  |  表示记录的语言的字符串。 如果未指定此参数，将使用默认语言代码分析记录。 <br/>请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>错误和警告
如果提供了不支持的语言代码，会生成错误且不提取关键短语。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

## <a name="see-also"></a>另请参阅

+ [预定义的技能](cognitive-search-predefined-skills.md)
+ [如何定义技能组合](cognitive-search-defining-skillset.md)