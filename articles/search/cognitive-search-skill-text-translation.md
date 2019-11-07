---
title: 文本转换认知技能（预览版）
titleSuffix: Azure Cognitive Search
description: 计算文本，对于每个记录，将返回在 Azure 认知搜索的 AI 扩充管道中转换为指定目标语言的文本。 此技能目前为公共预览版。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7c42c9033fac057c12426726a96ae6079f3080da
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715412"
---
#   <a name="text-translation-cognitive-skill"></a>文本翻译认知技能

> [!IMPORTANT] 
> 此技能目前为公共预览版。 提供的预览功能不带服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-preview](search-api-preview.md)提供了预览功能。 目前支持的门户支持不受支持，也没有 .NET SDK 支持。

**文本翻译**技能对文本进行评估，并针对每个记录返回已翻译为指定目标语言的文本。 此技能使用认知服务中提供的[文本翻译 API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate)。

此功能适用于你预期自己的文档可能不会全部采用一种语言的情况，在这种情况下，你可能会在编制索引以方便搜索之前通过翻译将文本标准化为单一语言。  这也适用于本地化用例，即，你可能需要将同一文本的副本以多种语言发布。

[文本翻译 API 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)是一种非区域认知服务，这意味着您的数据不一定与 Azure 认知搜索或附加认知服务资源位于同一区域。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 在认知服务中调用 Api 时，将会产生费用，并将其作为 Azure 认知搜索中文档解密阶段的一部分进行图像提取。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 [Azure 认知搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)上介绍了图像提取定价。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到文本翻译技能之前需要将其拆分，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入                | 说明 |
|---------------------|-------------|
| defaultToLanguageCode | （必需）将文档翻译为未显式指定语言的文档时所需的语言代码。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| defaultFromLanguageCode | （可选）将文档从未显式指定语言的文档进行翻译时所需的语言代码。  如果未指定 defaultFromLanguageCode，则会使用文本翻译 API 提供的自动语言检测功能来确定源语言。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| suggestedFrom | （可选）在 fromLanguageCode 输入和 defaultFromLanguageCode 参数均未提供且自动语言检测操作不成功情况下，将文档从源语言进行翻译所需的语言代码。  如果未指定 suggestedFrom 语言，则会将英语 (en) 用作 suggestedFrom 语言。 <br/> 请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |

## <a name="skill-inputs"></a>技能输入

| 输入名称     | 说明 |
|--------------------|-------------|
| text | 要翻译的文本。|
| toLanguageCode    | 一个表示文本翻译目标语言的字符串。 如果未指定此输入，则使用 defaultToLanguageCode 来翻译文本。 <br/>请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | 一个表示文本的当前语言的字符串。 如果此参数未指定，则会使用 defaultFromLanguageCode（在未提供 defaultFromLanguageCode 的情况下，则会使用自动语言检测功能）来翻译文本。 <br/>请参阅[支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 说明 |
|--------------------|-------------|
| translatedText | 进行从 translatedFromLanguageCode 到 translatedToLanguageCode 的文本翻译时获得的字符串结果。|
| translatedToLanguageCode  | 一个表示文本翻译目标语言代码的字符串。 适用于需要翻译为多种语言且需要能够跟踪哪些文本是哪种语言的情况。|
| translatedFromLanguageCode    | 一个表示文本翻译源语言代码的字符串。 适用于已选择启用自动语言检测选项的情况，因为这种情况下的输出会提供该检测的结果。|

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
如果为源语言或目标语言提供的语言代码不受支持，则会生成错误且文本不会进行翻译。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，则只会翻译前 50,000 个字符，并会发出警告。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
