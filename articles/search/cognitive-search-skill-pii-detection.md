---
title: PII 检测认知技能（预览）
titleSuffix: Azure Cognitive Search
description: 从 Azure 认知搜索中的丰富管道中的文本中提取和屏蔽个人身份信息。 此技能当前处于公共预览版中。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298877"
---
#    <a name="pii-detection-cognitive-skill"></a>PII 检测认知技能

> [!IMPORTANT] 
> 此技能当前处于公共预览版中。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持门户或 .NET SDK。

**PII 检测**技能从输入文本中提取个人身份信息，并允许您选择以各种方式从该文本中屏蔽这些信息。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

> [!NOTE]
> 随着通过增加处理频率、添加更多文档或添加更多 AI 算法来扩大范围，您需要[附加计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 认知搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50，000 个字符（以[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)） 如果需要在将数据发送到技能之前拆分数据，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写，所有参数都是可选的。

| 参数名称     | 描述 |
|--------------------|-------------|
| defaultLanguageCode |    输入文本的语言代码。 目前，仅`en`支持。 |
| minimumPrecision | 介于 0.0 和 1.0 之间的值。 如果置信度分数（在`piiEntities`输出中）低于设定`minimumPrecision`值，则不返回或屏蔽实体。 默认值为 0.0。 |
| 遮蔽模式 | 提供各种方法在输入文本中屏蔽检测到的 PII 的参数。 可以使用以下选项： <ul><li>`none`（默认）：这意味着不会执行任何掩蔽，`maskedText`也不会返回输出。 </li><li> `redact`：此选项将从输入文本中删除检测到的实体，而不是将其替换为任何内容。 请注意，在这种情况下，输出中的`piiEntities`偏移量将与原始文本相关，而不是与蒙版文本相关。 </li><li> `replace`： 此选项将用`maskingCharacter`参数中给出的字符替换检测到的实体。  字符将重复到检测到的实体的长度，以便偏移量将正确对应于输入文本和输出`maskedText`。</li></ul> |
| 遮蔽字符 | 如果参数设置为，则用于屏蔽文本的`maskingMode`字符。 `replace` 支持以下选项： `*` （默认）， `#`. `X` 仅当未设置为`null``maskingMode``replace`时，才能使用此参数。 |


## <a name="skill-inputs"></a>技能输入

| 输入名称      | 描述                   |
|---------------|-------------------------------|
| languageCode    | 可选。 默认为 `en`。  |
| text          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

| 输出名称      | 描述                   |
|---------------|-------------------------------|
| 皮实体 | 复杂类型的数组，包含以下字段： <ul><li>文本（提取的实际 PII）</li> <li>type</li><li>亚</li><li>分数（值越高，意味着它更有可能是一个真正的实体）</li><li>偏移量（输入文本中）</li><li>长度</li></ul> </br> [可在此处找到可能的类型和子类型。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| 蒙面文本 | 如果`maskingMode`设置为 以外的`none`值，则此输出将是所选`maskingMode`文本上执行的掩蔽的字符串结果。  如果`maskingMode`设置为`none`，则此输出将不存在。 |

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

请注意，此技能输出中实体返回的偏移量直接从[文本分析 API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)返回，这意味着如果您使用它们索引到原始字符串中，则应使用 .NET 中的[StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)类来提取正确的内容。  [更多详情可在此处找到。](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>错误和警告案例
如果不支持文档的语言代码，则返回警告，并且不提取任何实体。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

如果技能返回警告，则输出`maskedText`可能为空。  这意味着，如果您希望输出存在，以便输入到以后的技能中，则该输出将不能按预期工作。 编写技能集定义时，请记住这一点。

## <a name="see-also"></a>请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
