---
title: 语言检测认知搜索技能 - Azure 搜索
description: 计算非结构化的文本，并针对每个文本，返回语言标识符和表示 Azure 搜索扩充管道中分析长度的得分。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 14163b959a6e91406133ca2f5a125c7e2df967ad
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635812"
---
#   <a name="language-detection-cognitive-skill"></a>语言检测认知技能

语言检测技能检测输入文本的语言，并报告在请求中提交的每个文档的单一语言代码。 语言代码配有表示分析长度的得分。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

当需要提供文本的语言作为其他技能（例如，[情绪分析技能](cognitive-search-skill-sentiment.md)或[文本拆分技能](cognitive-search-skill-textsplit.md)）的输入时，此功能尤其有用。

语言检测利用必应的自然语言处理库，此类库超出为文本分析列出的[受支持的语言和区域](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)的数目。 语言的具体列表未发布，但包含所有广泛传播的语言，以及变体、方言和某些区域性的和文化性的语言。 如果你的内容是采用不常用的语言表达的，可以[尝试语言检测 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)，看是否会返回一个代码。 无法检测的语言的响应为 `unknown`。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API，以及在 Azure 搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为50000个字符[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果在将数据发送到情绪分析器之前需要拆分数据，可以使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

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
