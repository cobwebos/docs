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
ms.date: 10/09/2020
ms.author: aahi
ms.openlocfilehash: 570a21a307d60ab1e2c02d6481746576f5dcf0e3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930282"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>如何：使用文本分析 API 检测情绪

文本分析 API 的情绪分析功能评估文本并返回每个句子的情绪分数和标签。 这有助于检测社交媒体、客户评价、论坛等内容中的积极和消极情绪。 API 使用的 AI 模型由该服务提供，只需发送内容即可进行分析。

发送情绪分析请求后，API 会在句子和文档级别返回情绪标签（如“消极”、“中性”和“积极”）和置信度分数。

情绪分析支持多种语言，并在预览版中提供了更多的语言。 有关详细信息，请参阅[支持的语言](../text-analytics-supported-languages.md)。

## <a name="sentiment-analysis-versions-and-features"></a>情绪分析版本和功能

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Feature                                   | 情绪分析 v3 | 情绪分析 v3.1（预览） |
|-------------------------------------------|-----------------------|-----------------------------------|
| 用于单个请求和批量请求的方法    | X                     | X                                 |
| 情绪分数和标记             | X                     | X                                 |
| 基于 Linux 的 [Docker 容器](text-analytics-how-to-install-containers.md) | X  |  |
| 观点挖掘                            |                       | X                                 |

### <a name="sentiment-scoring-and-labeling"></a>情绪评分和标记

情绪分析 v3 将情绪标签应用于文本，然后在句子和文档级别返回标签，每个标签都有一个置信度分数。 

标签为积极、消极和中性。 在文档级别，还可能返回混合情绪标签。 文档的情绪由以下内容确定：

| 句子情绪                                                                            | 返回的文档标签 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 文档中至少有一个 `positive` 句子。 剩下的句子为 `neutral`。 | `positive`              |
| 文档中至少有一个 `negative` 句子。 剩下的句子为 `neutral`。 | `negative`              |
| 文档中至少有一个 `negative` 句子和一个 `positive` 句子。    | `mixed`                 |
| 文档中的所有句子为 `neutral`。                                                  | `neutral`               |

置信度分数范围介于 1 到 0 之间。 分数越接近于 1 表示标签分类的置信度越高，分数越低表示置信度越低。 对于每个文档或每个句子，与标签（积极、消极和中性）关联的预测分数总和为 1。

### <a name="opinion-mining"></a>观点挖掘

观点挖掘是情绪分析的一项功能，从版本 3.1-preview.1 开始提供。 此功能在自然语言处理 (NLP) 中也称为基于方面的情绪分析，它更加精细地描述了对文本中某些方面（例如产品或服务的属性）的观点。

例如，如果客户评论某个酒店，例如“房间很好，但员工不友好”，观点挖掘将查找文本中的各个方面及其相关的看法和情绪：

| 方面 | 观点    | 情绪 |
|--------|------------|-----------|
| 房间   | 很好      | 积极  |
| staff  | 不友好 | 消极  |

若要在结果中获取观点挖掘，必须在情绪分析请求中包含 `opinionMining=true` 标志。 观点挖掘结果将包含在情绪分析响应中。

## <a name="sending-a-rest-api-request"></a>发送 REST API 请求 

### <a name="preparation"></a>准备工作

当为情绪分析提供较少的文本时，会得到更高质量的结果。 这与关键短语提取相反，关键短语提取在处理较大的文本块时表现更好。 要从两个操作获取最佳结果，请考虑相应地重建输入。

必须拥有以下格式的 JSON 文档：ID、文本和语言

每个文档的大小必须少于 5,120 个字符， 每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。

## <a name="structure-the-request"></a>构造请求

创建 POST 请求。 可[使用 Postman](text-analytics-how-to-call-api.md) 或以下参考链接中的“API 测试控制台”来快速构建并发送请求。 

#### <a name="version-31-preview1"></a>[版本 3.1-preview.1](#tab/version-3-1)

[情绪分析 v3.1 参考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

[情绪分析 v3 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>请求终结点

使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTPS 终结点，以便进行情绪分析。 必须包括要使用的版本的正确 URL。 例如：

> [!NOTE]
> 可以在 Azure 门户上找到文本分析资源的密钥和终结点。 它们将位于资源的“快速启动”页上的“资源管理”下。 

#### <a name="version-31-preview1"></a>[版本 3.1-preview.1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

若要获取观点挖掘结果，必须包含 `opinionMining=true` 参数。 例如：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

默认情况下，此参数设置为 `false`。 

#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

发送请求标头以包括文本分析 API 密钥。 在请求正文中，提供为此分析准备的 JSON 文档集合。

### <a name="example-sentiment-analysis-request"></a>情绪分析请求示例 

下面是可能提交用于情绪分析的内容示例。 两个版本的请求格式相同。
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

文本分析 API 是无状态的。 不会在帐户中存储数据，结果会立即在响应中返回。


### <a name="view-the-results"></a>查看结果

情绪分析为整个文档以及其中的每个句子返回情绪标签和置信度分数。 分数越接近于 1 表示标签分类的置信度越高，分数越低表示置信度越低。 一个文档可以有多个句子，每个文档或句子的置信度分数合计为 1。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中。 然后，将输出导入到可以用来对数据进行排序、搜索和操作的应用程序。 由于多语言和表情符号支持，响应可能包含文本偏移。 有关详细信息，请参阅[如何处理偏移](../concepts/text-offsets.md)。

#### <a name="version-31-preview1"></a>[版本 3.1-preview.1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>情绪分析 v3.1 示例响应

除了“版本 3.0”选项卡中的响应对象之外，情绪分析 v3.1 还提供了观点挖掘。在下面的回复中，“餐厅的食物很好，并且服务员很友好”这句话包括两个方面：食物和服务员  。 每个方面的 `relations` 属性都包含一个 `ref` 值，其中包含对相关 `documents`、`sentences` 和 `opinions` 对象的 URI 引用。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>情绪分析 v3.0 示例响应

情绪分析 v3 的响应包含每个已分析句子和文档的情绪标签和分数。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>总结

本文介绍了使用文本分析 API 进行情绪分析的概念和工作流。 综上所述：

+ 情绪分析适用于选定的语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/sentiment` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
+ 响应输出包含每个文档 ID 的情绪得分，可流式传输到接受 JSON 的任何应用。 例如，Excel 和 Power BI。

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [使用文本分析客户端库](../quickstarts/text-analytics-sdk.md)
* [新增功能](../whats-new.md)
