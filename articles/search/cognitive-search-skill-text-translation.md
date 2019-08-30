---
title: 文本转换认知搜索技能-Azure 搜索
description: 计算文本, 对于每个记录, 将返回在 Azure 搜索扩充管道中转换为指定目标语言的文本。
services: search
manager: nitinme
author: careyjmac
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cmacdo
ms.subservice: cognitive-search
ms.openlocfilehash: 407fd246313e582a3d28a6cbb474edefecd7f0af
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183327"
---
#   <a name="text-translation-cognitive-skill"></a>文本转换认知技能

**文本翻译**技能计算文本, 对于每个记录, 将返回转换为指定目标语言的文本。 此技能使用认知服务中提供的[文本翻译 API 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) 。

如果你希望你的文档不能全部使用一种语言, 则此功能非常有用, 在这种情况下, 你可以通过转换文本来将文本标准化为单一语言, 然后再对其进行索引。  它还可用于本地化用例, 在这种情况下, 你可能想要在多种语言中使用相同的文本副本。

[文本翻译 API 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)是一种非区域认知服务, 这意味着不保证你的数据与 Azure 搜索或附加认知服务资源位于同一区域。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API，以及在 Azure 搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。

## <a name="odatatype"></a>@odata.type  
TranslationSkill。

## <a name="data-limits"></a>数据限制
记录的最大大小应为50000个字符[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果你需要在将数据发送到文本翻译技能之前分解你的数据, 请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入                | 描述 |
|---------------------|-------------|
| defaultToLanguageCode | 请求用于将文档翻译为的语言代码, 用于未显式指定为语言的文档。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| defaultFromLanguageCode | 可有可无用于转换文档的语言代码, 用于未显式指定 from 语言的文档。  如果未指定 defaultFromLanguageCode, 则将使用文本翻译 API 提供的自动语言检测来确定发件人语言。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| suggestedFrom | 可有可无当 fromLanguageCode 输入和 defaultFromLanguageCode 参数均未提供时用于转换文档的语言代码, 自动语言检测不成功。  如果未指定 suggestedFrom 语言, 则将使用英语 (en) 作为 suggestedFrom 语言。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |

## <a name="skill-inputs"></a>技能输入

| 输入名称     | 描述 |
|--------------------|-------------|
| text | 要转换的文本。|
| toLanguageCode    | 指示文本应转换为的语言的字符串。 如果未指定此输入, 则 defaultToLanguageCode 将用于转换文本。 <br/>请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | 指示文本的当前语言的字符串。 如果未指定此参数, 则将使用 defaultFromLanguageCode (或自动语言检测, 如果未提供 defaultFromLanguageCode) 来转换文本。 <br/>请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 描述 |
|--------------------|-------------|
| translatedText | 从 translatedFromLanguageCode 到 translatedToLanguageCode 的文本转换的字符串结果。|
| translatedToLanguageCode  | 一个字符串, 指示文本转换为的语言代码。 如果要翻译为多种语言, 并希望能够跟踪哪些文本是哪种语言, 这会很有用。|
| translatedFromLanguageCode    | 一个字符串, 该字符串指示文本所转换为的语言代码。 如果你选择了自动语言检测选项, 则此功能非常有用, 因为此输出会为你提供该检测的结果。|

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
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
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>错误和警告
如果为 from 或 to 语言提供不受支持的语言代码, 则会生成错误, 并且不会转换文本。
如果你的文本为空，则不会生成警告。
如果文本超过50000个字符, 则只会转换前50000个字符并发出警告。

## <a name="see-also"></a>请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
