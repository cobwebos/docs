---
title: PII 检测认知技能（预览版）
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中的扩充管道中，从文本中提取并屏蔽个人身份信息。 此技能目前为公共预览版。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298877"
---
#    <a name="pii-detection-cognitive-skill"></a>PII 检测认知技能

> [!IMPORTANT] 
> 此技能目前为公共预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持门户或 .NET SDK。

**PII 检测**技巧从输入文本中提取个人身份信息，并提供以各种方式从该文本中屏蔽该文本的选项。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

> [!NOTE]
> 当你通过提高处理频率、添加更多文档或添加更多 AI 算法来扩展作用域时，你将需要[附加可计费认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 认知搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为50000个字符[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果需要在将数据发送到技能之前分解数据，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写，所有参数都是可选的。

| 参数名称     | 说明 |
|--------------------|-------------|
| defaultLanguageCode |    输入文本的语言代码。 目前仅`en`支持。 |
| minimumPrecision | 介于0.0 和1.0 之间的值。 如果置信度（ `piiEntities`输出中）低于设置`minimumPrecision`值，则不会返回或屏蔽该实体。 默认值为 0.0。 |
| maskingMode | 提供多种方法来屏蔽输入文本中检测到的 PII 的参数。 可以使用以下选项： <ul><li>`none`（默认值）：这意味着不会执行任何屏蔽，并且`maskedText`不会返回输出。 </li><li> `redact`：此选项将从输入文本中删除检测到的实体，而不会将其替换为任何内容。 请注意，在这种情况下， `piiEntities`输出中的偏移量将与原始文本（而非掩码文本）相关。 </li><li> `replace`：此选项会将检测到的实体替换为`maskingCharacter`参数中给定的字符。  字符将重复到检测到的实体长度，以便与输入文本和输出`maskedText`正确对应的偏移量。</li></ul> |
| maskingCharacter | 如果`maskingMode`参数设置为`replace`，则为用于屏蔽文本的字符。 支持以下选项： `*` （默认值）、 `#`、。 `X` 仅`null`当`maskingMode`未设置为`replace`时，此参数才可用。 |


## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| languageCode    | 可选。 默认值为 `en`。  |
| text          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

| 输出名称      | 说明                   |
|---------------|-------------------------------|
| piiEntities | 复杂类型的数组，包含以下字段： <ul><li>text （提取的实际 PII）</li> <li>type</li><li>类型</li><li>评分（较高的值意味着更可能成为真实实体）</li><li>偏移量（输入文本中）</li><li>长度</li></ul> </br> [可在此处找到可能的类型和子类型。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| maskedText | 如果`maskingMode`将设置为以外的值`none`，则此输出将是对输入文本执行的掩码的字符串结果，如所选`maskingMode`的所述。  如果`maskingMode`设置为`none`，则不会出现此输出。 |

##    <a name="sample-definition"></a>示例定义

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>示例输入

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>示例输出

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

请注意，为此技能的输出中的实体返回的偏移量直接从[文本分析 API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)返回，这意味着，如果您使用它们来索引原始字符串，则应使用 .net 中的[system.globalization.stringinfo>](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)类来提取正确的内容。  [可在此处找到更多详细信息。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>错误和警告情况
如果文档的语言代码不受支持，则会返回警告且不会提取实体。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

如果技能返回警告，则输出`maskedText`可能为空。  这意味着，如果你希望输出可用于输入更高的技能，则不会按预期方式工作。 编写技能组合定义时，请牢记这一点。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
