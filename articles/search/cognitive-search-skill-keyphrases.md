---
title: 关键短语提取认知技能
titleSuffix: Azure Cognitive Search
description: 计算非结构化的文本，并针对每个记录返回 Azure 认知搜索的 AI 扩充管道中的关键短语列表。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 529e79abbd7fa8f9733254d207af570237044305
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080815"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>关键短语提取认知技能

关键短语提取技能可以计算非结构化的文本，并针对每个记录返回关键短语列表。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

如果你需要快速确定记录中的谈话要点，此功能十分有用。 例如，给定输入文本“The food was delicious and there were wonderful staff”，服务会返回“food”和“wonderful staff”。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到关键短语提取器之前需要拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入                | 说明 |
|---------------------|-------------|
| `defaultLanguageCode` | （可选）要应用到未显式指定语言的文档的语言代码。  如果未指定默认语言代码，会将英语 (en) 用作默认语言代码。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)。 |
| `maxKeyPhraseCount`   | （可选）要生成的关键短语的最大数量。 |

## <a name="skill-inputs"></a>技能输入

| 输入  | 说明 |
|--------------------|-------------|
| `text` | 要分析的文本。|
| `languageCode`    |  表示记录的语言的字符串。 如果未指定此参数，将使用默认语言代码分析记录。 <br/>请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

## <a name="skill-outputs"></a>技能输出

| 输出     | 描述 |
|--------------------|-------------|
| `keyPhrases` | 从输入文本中提取的关键短语的列表。 关键短语按重要性顺序返回。 |


##  <a name="sample-definition"></a>示例定义

考虑具有以下字段的 SQL 记录：

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

然后，你的技能定义可能如下所示：

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
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

##  <a name="sample-output"></a>示例输出

对于上面的示例，你的技能的输出将写入到被称为 "document/myKeyPhrases" 的扩充树中的新节点，因为这是 `targetName` 我们指定的。 如果未指定，则 `targetName` 它将是 "document/keyPhrases"。

#### <a name="documentmykeyphrases"></a>document/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

您可以使用 "document/myKeyPhrases" 作为其他技能的输入，或者使用作为[输出字段映射](cognitive-search-output-field-mapping.md)的源。

## <a name="errors-and-warnings"></a>错误和警告
如果提供了不支持的语言代码，会生成错误且不提取关键短语。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何定义输出字段映射](cognitive-search-output-field-mapping.md)
