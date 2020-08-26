---
title: PII 检测认知技能（预览版）
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中的扩充管道中，从文本中提取并屏蔽个人身份信息。 此技能目前为公共预览版。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: bec993c2b59aa03195b78a02668baf3f5fac6695
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080743"
---
#    <a name="pii-detection-cognitive-skill"></a>PII 检测认知技能

> [!IMPORTANT] 
> 此技能目前为公共预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持门户或 .NET SDK。

**PII 检测**技能可以从输入文本中提取个人身份信息，并可让你通过多种方式在该文本中屏蔽此类信息。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到技能之前需要将其拆分，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

这些参数区分大小写并且都是可选的。

| 参数名称     | 说明 |
|--------------------|-------------|
| `defaultLanguageCode` |    输入文本的语言代码。 目前仅支持 `en`。 |
| `minimumPrecision` | 一个介于 0.0 和 1.0 之间的值。 如果置信度分数（在 `piiEntities` 输出中）低于所设置的 `minimumPrecision` 值，则不会返回或屏蔽该实体。 默认值为 0.0。 |
| `maskingMode` | 一个提供多种方法来屏蔽输入文本中检测到的 PII 的参数。 可以使用以下选项： <ul><li>`none`（默认值）：这意味着不会执行任何屏蔽，并且不会返回 `maskedText` 输出。 </li><li> `redact`：此选项将从输入文本中删除检测到的实体，并且不会将其替换为任何内容。 请注意，在这种情况下，`piiEntities` 输出中的偏移量将与原始文本（而非已屏蔽文本）相关。 </li><li> `replace`：此选项将用 `maskingCharacter` 参数中给定的字符替换检测到的实体。  将重复该字符，直至达到检测到的实体的长度，以便偏移量与输入文本和输出 `maskedText` 都正确对应。</li></ul> |
| `maskingCharacter` | 当 `maskingMode` 参数设置为 `replace` 时，将用来屏蔽文本的字符。 支持以下选项：`*`（默认值）、`#`、`X`。 如果 `maskingMode` 未设置为 `replace`，则此参数只能为 `null`。 |


## <a name="skill-inputs"></a>技能输入

| 输入名称      | 描述                   |
|---------------|-------------------------------|
| `languageCode`    | 可选。 默认值为 `en`。  |
| `text`          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

| 输出名称      | 说明                   |
|---------------|-------------------------------|
| `piiEntities` | 复杂类型的数组，包含以下字段： <ul><li>text（提取的实际 PII）</li> <li>type</li><li>subType</li><li>score（值越高意味着它越有可能是一个真实的实体）</li><li>offset（输入文本中）</li><li>length</li></ul> </br> [可在此处找到可能的类型和子类型。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| `maskedText` | 如果 `maskingMode` 设置为 `none` 以外的值，则此输出将是对由所选 `maskingMode` 描述的输入文本执行屏蔽后的字符串结果。  如果 `maskingMode` 设置为 `none`，则不会提供此输出。 |

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

请注意，在此技能的输出中，针对实体返回的偏移量是直接从[文本分析 API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) 返回的，这意味着如果使用这些偏移量为原始字符串编制索引，则应使用 .NET 中的 [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) 类来提取正确的内容。  [可在此处找到更多详细信息。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>错误和警告案例
如果文档的语言代码不受支持，则会返回警告，并且不提取任何实体。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

如果技能返回警告，则输出 `maskedText` 可能为空。  这意味着，如果你希望输出存在，以便输入到以后的技能中，它将无法按预期工作。 编写技能组定义时，请牢记这一点。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
