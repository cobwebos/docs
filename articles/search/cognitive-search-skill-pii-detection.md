---
title: PII 检测认知技能（预览版）
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中从扩充管道中的文本提取和屏蔽个人信息。 此技能目前为公共预览版。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: acacf617d3f1d9ab891d08b32fc2dfb14deb64a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540517"
---
# <a name="pii-detection-cognitive-skill"></a>PII 检测认知技能

> [!IMPORTANT] 
> 此技能目前为公共预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持门户或 .NET SDK。

**PII 检测**技巧从输入文本中提取个人信息，并提供对其进行屏蔽的选项。 此技能使用认知服务中的[文本分析](../cognitive-services/text-analytics/overview.md)提供的机器学习模型。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)所述。

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>数据限制

记录的最大大小应为 50,000 个字符，通过 [`String.Length`](/dotnet/api/system.string.length) 进行测量。 如果在将数据发送给技能之前需要将数据分块，请考虑使用 [文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

这些参数区分大小写并且都是可选的。

| 参数名称     | 说明 |
|--------------------|-------------|
| `defaultLanguageCode` |    输入文本的语言代码。 目前仅支持 `en`。 |
| `minimumPrecision` | 一个介于 0.0 和 1.0 之间的值。 如果置信度分数（在 `piiEntities` 输出中）低于所设置的 `minimumPrecision` 值，则不会返回或屏蔽该实体。 默认值为 0.0。 |
| `maskingMode` | 提供多种方法来屏蔽输入文本中检测到的个人信息的参数。 可以使用以下选项： <ul><li>`none` (默认) ：不会发生掩码，并且 `maskedText` 不会返回输出。 </li><li> `redact`：从输入文本中删除检测到的实体，而不会替换删除的值。 在这种情况下，输出中的偏移量 `piiEntities` 将与原始文本（而非掩码文本）相关。 </li><li> `replace`：用参数中给定的字符替换检测到的实体 `maskingCharacter` 。 将重复该字符，直至达到检测到的实体的长度，以便偏移量与输入文本和输出 `maskedText` 都正确对应。</li></ul> |
| `maskingCharacter` | 如果 `maskingMode` 参数设置为，则为用于屏蔽文本的字符 `replace` 。 支持以下选项：`*`（默认值）、`#`、`X`。 如果 `maskingMode` 未设置为 `replace`，则此参数只能为 `null`。 |

## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                   |
|---------------|-------------------------------|
| `languageCode`    | 可选。 默认值为 `en`。  |
| `text`          | 要分析的文本。          |

## <a name="skill-outputs"></a>技能输出

| 输出名称      | 说明                   |
|---------------|-------------------------------|
| `piiEntities` | 复杂类型的数组，包含以下字段： <ul><li>text（提取的实际 PII）</li> <li>type</li><li>subType</li><li>score（值越高意味着它越有可能是一个真实的实体）</li><li>offset（输入文本中）</li><li>length</li></ul> </br> [可在此处找到可能的类型和子类型。](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | 如果 `maskingMode` 设置为 `none` 以外的值，则此输出将是对由所选 `maskingMode` 描述的输入文本执行屏蔽后的字符串结果。  如果 `maskingMode` 设置为 `none`，则不会提供此输出。 |

## <a name="sample-definition"></a>示例定义

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

## <a name="sample-input"></a>示例输入

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

## <a name="sample-output"></a>示例输出

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

对于此技能的输出中的实体，返回的偏移量直接从 [文本分析 API](../cognitive-services/text-analytics/overview.md)返回，这意味着，如果您使用它们来索引原始字符串，则应使用 .net 中的 [system.globalization.stringinfo>](/dotnet/api/system.globalization.stringinfo) 类来提取正确的内容。  [可在此处找到更多详细信息。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>错误和警告

如果文档的语言代码不受支持，则会返回警告，并且不提取任何实体。
如果文本为空，则返回警告。
如果文本大于 50,000 个字符，只会分析前 50,000 个字符，并会发出警告。

如果技能返回警告，则输出可能为 `maskedText` 空，这可能会影响任何预期输出的下游技能。 出于此原因，请确保在写入技能组合定义时调查与缺少的输出相关的所有警告。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)