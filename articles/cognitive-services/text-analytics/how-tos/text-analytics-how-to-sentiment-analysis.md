---
title: 文本分析 REST API 执行情绪分析
titleSuffix: Azure Cognitive Services
description: 本文介绍如何通过 Azure 认知服务文本分析 REST API 检测文本中的情绪。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/27/2020
ms.author: aahi
ms.openlocfilehash: 99a62daf6dced88efd9bda591a0ca44a8b259a75
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195632"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>如何：使用文本分析 API 检测情绪

文本分析 API 的情绪分析功能评估文本并返回每个句子的情绪分数和标签。 这有助于检测社交媒体、客户评价、论坛等内容中的积极和消极情绪。 API 使用的 AI 模型由该服务提供，只需发送内容即可进行分析。

> [!TIP]
> 文本分析还提供一个基于 Linux 的 Docker 容器映像，用于检测语言，因此可以在靠近数据的位置[安装并运行文本分析容器](text-analytics-how-to-install-containers.md)。

情绪分析支持多种语言，并在预览版中提供了更多的语言。 有关详细信息，请参阅[支持的语言](../text-analytics-supported-languages.md)。

## <a name="concepts"></a>概念

文本分析 API 使用机器学习分类算法生成介于 0 到 1 之间的情绪分数。 接近 1 的评分表示积极情绪，接近 0 的评分表示消极情绪。 情绪分析针对整个文档进行，而不是针对文本中的各个实体进行。 这意味着将返回文档或句子级别的情绪分数。 

使用的模型通过含有文本和情绪关联内容的大量语料库进行了预先训练。 它使用多种技术进行分析，包括文本处理、词性分析、词序和字词关联。 有关该算法的详细信息，请参阅[文本分析简介](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)。 目前，不支持提供用户自己的训练数据。 

当文档只包含一些句子而不是大段文本时，评分准确性有提高的趋势。 在客观性评估阶段，模型会确定整个文档是客观内容还是包含情感。 客观内容占主导的文档将不会进入情绪检测阶段，将获得 0.50 分数，不会进行进一步处理。 对于继续处理的文档，下一阶段会得出高于或低于 0.50 的分数， 具体取决于文档中检测到的情绪程度。

## <a name="sentiment-analysis-versions-and-features"></a>情绪分析版本和功能

文本分析 API 提供两个版本的情绪分析 - v2 和 v3。 情绪分析 v3（公共预览版）在 API 的文本分类和计分的准确度和细节方面有了显著改进。

> [!NOTE]
> * 情绪分析 v3 的请求格式和[数据限制](../overview.md#data-limits)与上一版本相同。
> * 情绪分析 v3 供以下区域使用：`Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe` 和 `West US 2`。

| Feature                                   | 情绪分析 v2 | 情绪分析 v3 |
|-------------------------------------------|-----------------------|-----------------------|
| 用于单个请求和批量请求的方法    | X                     | X                     |
| 整篇文档的情绪分数  | X                     | X                     |
| 各个句子的情绪分数 |                       | X                     |
| 情绪标记                        |                       | X                     |
| 模型版本控制                   |                       | X                     |

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

### <a name="sentiment-scoring"></a>情绪评分

情绪分析 v3 使用情绪标签对文本进行分类（如下所述）。 返回的分数表示模型的置信度，即文本为积极、消极或中性。 分数越高，置信度越高。 

### <a name="sentiment-labeling"></a>情绪标记

情绪分析 v3 会返回句子和文档级别的情绪标签（`positive`、`negative` 和 `neutral`）以及置信度分数。 也可以在文档级别返回 `mixed` 情绪标签。 

文档的情绪由以下内容确定：

| 句子情绪                                                                            | 返回的文档标签 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 文档中至少有一个 `positive` 句子。 剩下的句子为 `neutral`。 | `positive`              |
| 文档中至少有一个 `negative` 句子。 剩下的句子为 `neutral`。 | `negative`              |
| 文档中至少有一个 `negative` 句子和一个 `positive` 句子。    | `mixed`                 |
| 文档中的所有句子为 `neutral`。                                                  | `neutral`               |

### <a name="model-versioning"></a>模型版本控制

> [!NOTE]
> 从版本 `v3.0-preview.1` 开始，提供用于情感分析的模型版本控制。

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>示例 C# 代码

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs) 上查找一个可调用此版情绪分析的示例 C# 应用程序。


#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>情绪评分

情绪分析器将文本分类为积极为主或消极为主， 并分配范围在 0 到 1 之间的分数。 接近 0.5 的值表示中性或不确定。 得分 0.5 表示中性。 如果无法分析字符串的情绪或不含情绪，则分数始终为 0.5。 例如，如果传入带有英语语言代码的西班牙语字符串，则分数为 0.5。

---

## <a name="sending-a-rest-api-request"></a>发送 REST API 请求 

### <a name="preparation"></a>准备工作

当为情绪分析提供较少的文本时，会得到更高质量的结果。 这与关键短语提取相反，关键短语提取在处理较大的文本块时表现更好。 要从两个操作获取最佳结果，请考虑相应地重建输入。

必须拥有以下格式的 JSON 文档：ID、文本和语言

每个文档的大小必须少于 5,120 个字符， 每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。

## <a name="structure-the-request"></a>构造请求

创建 POST 请求。 可[使用 Postman](text-analytics-how-to-call-api.md) 或以下参考链接中的“API 测试控制台”来快速构建并发送请求  。 

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

[情绪分析 v3 参考](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

[情绪分析 v2 参考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTPS 终结点，以便进行情绪分析。 必须包括要使用的版本的正确 URL。 例如：

> [!NOTE]
> 可以在 Azure 门户上找到文本分析资源的密钥和终结点。 它们将位于资源的“快速启动”  页上的“资源管理”  下。 

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

发送请求标头以包括文本分析 API 密钥。 在请求正文中，提供为此分析准备的 JSON 文档集合。

### <a name="example-sentiment-analysis-request"></a>情绪分析请求示例 

下面是可能提交用于情绪分析的内容示例。 两个版本的 API 的请求格式相同。
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

文本分析 API 是无状态的。 不会在帐户中存储数据，结果会立即在响应中返回。


### <a name="view-the-results"></a>查看结果

情绪分析器将文本分类为积极为主或消极为主， 并分配范围在 0 到 1 之间的分数。 接近 0.5 的值表示中性或不确定。 得分 0.5 表示中性。 如果无法分析字符串的情绪或不含情绪，则分数始终为 0.5。 例如，如果传入带有英语语言代码的西班牙语字符串，则分数为 0.5。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中。 然后，将输出导入到可以用来对数据进行排序、搜索和操作的应用程序。 由于多语言和表情符号支持，响应可能包含文本偏移。 有关详细信息，请参阅[如何处理偏移](../concepts/text-offsets.md)。

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>情绪分析 v3 示例响应

情绪分析 v3 的响应包含每个已分析句子和文档的情绪标签和分数。 如果文档情绪标签为 `mixed`，则不会返回 `documentScores`。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>情绪分析 v2 示例响应

情绪分析 v2 的响应包含每个已发送文档的情绪分数。

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>总结

本文介绍了使用文本分析 API 进行情绪分析的概念和工作流。 综上所述：

+ 情绪分析适用于选定的语言并提供两个版本。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/sentiment` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
+ 响应输出包含每个文档 ID 的情绪得分，可流式传输到接受 JSON 的任何应用。 例如，Excel 和 Power BI。

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [使用文本分析客户端库](../quickstarts/text-analytics-sdk.md)
* [新增功能](../whats-new.md)
