---
title: 语言检测认知搜索技能 - Azure 搜索
description: 计算非结构化的文本，并针对每个文本，返回语言标识符和表示 Azure 搜索扩充管道中分析长度的得分。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341618"
---
#   <a name="language-detection-cognitive-skill"></a>语言检测认知技能

**语言检测**技能检测到的语言输入文本并报告在请求上提交的每个文档的单个语言代码。 语言代码配有表示分析长度的得分。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

当需要提供文本的语言作为其他技能（例如，[情绪分析技能](cognitive-search-skill-sentiment.md)或[文本拆分技能](cognitive-search-skill-textsplit.md)）的输入时，此功能尤其有用。

语言检测利用必应的自然语言处理库，它超出了数的[支持的语言和区域](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)列出文本分析。 语言的确切列表未发布，但包括所有广泛所说的语言，以及变体、 方言和某些区域和文化的语言。 如果有较少使用的语言所表示的内容，则可以[试用语言检测 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)以查看是否返回代码。 不能检测到的语言的响应是`unknown`。

> [!NOTE]
> 从 2018 年 12 月 21 日开始，可以[将认知服务资源附加到 Azure 搜索技能集](cognitive-search-attach-cognitive-services.md)。 这会使我们能够开始对技能集执行收费。 在此日期，我们还会开始将图像提取视为文档破解阶段的一部分进行计费。 我们将继续提供文档文本提取服务而不收取额外费用。
>
> 执行[内置认知技能](cognitive-search-predefined-skills.md)按[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services)收费，其费率与你以往直接执行任务的费率相同。 图像提取是 Azure 搜索收费项，目前以预览定价提供。 有关详细信息，请参见 [Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)或[如何计费](search-sku-tier.md#how-billing-works)。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 `String.Length` 进行测量。 如果在将数据发送到情绪分析器之前需要拆分数据，可以使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-inputs"></a>技能输入

参数区分大小写。

| 输入     | 说明 |
|--------------------|-------------|
| text | 要分析的文本。|

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 说明 |
|--------------------|-------------|
| languageCode | 标识语言的 ISO 6391 语言代码。 例如，“en”。 |
| languageName | 语言的名称。 例如，“英语”。 |
| 得分 | 一个介于 0 和 1 之间的值。 正确标识语言的可能性。 如果句子中有混合语言，得分可能会低于 1。  |

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
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
```


##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>错误案例
如果以不支持的语言表达文本，会生成错误，且不返回任何语言标识符。

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
