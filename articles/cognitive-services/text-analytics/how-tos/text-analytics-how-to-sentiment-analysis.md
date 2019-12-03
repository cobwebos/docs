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
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: 33c9c708adcc196bc7d9b2e8a066d18e4dd20608
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326621"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>示例：使用文本分析检测情绪

[Azure 情绪分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) 评估每个文档的文本输入并返回情绪分数。 分数范围为从 0（消极）到 1（积极）。

此功能对于检测社交媒体、客户评论和论坛中的积极和消极情绪非常有用。 内容由你提供。 服务提供模型和定型数据。

目前，情绪分析 API 支持英语、德语、西班牙语和法语。 其他语言以预览版提供。 有关详细信息，请参阅[支持的语言](../text-analytics-supported-languages.md)。

> [!TIP]
> Azure 文本分析 API 还提供一个基于 Linux 的 Docker 容器映像，用于进行情绪分析，因此可以在靠近数据的位置[安装并运行文本分析容器](text-analytics-how-to-install-containers.md)。

## <a name="concepts"></a>概念

文本分析使用机器学习分类算法生成介于 0 到 1 之间的情绪分数。 接近 1 的评分表示积极情绪，接近 0 的评分表示消极情绪。 该模型通过大量含有情绪关联的文本进行了预先定型。 目前，不支持提供用户自己的训练数据。 该模型在文本分析过程中使用了多种技术， 包括文本处理、词性分析、词序和字词关联。 有关该算法的详细信息，请参阅[文本分析简介](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)。

对整个文档执行情绪分析，而不是提取文本中特定实体的情绪。 实际上，当文档只包含一个或两个句子而不是大段文本时，评分准确性有提高的趋势。 在客观性评估阶段，模型会确定整个文档是客观内容还是包含情感。 客观内容占主导的文档将不会进入情绪检测阶段，将获得 0.50 分数，不会进行进一步处理。 对于继续处理的文档，下一阶段会得出高于或低于 0.50 的分数， 具体取决于文档中检测到的情绪程度。

## <a name="sentiment-analysis-v3-public-preview"></a>情绪分析 v3 公共预览版

下一版本的情绪分析现在提供公共预览版， 在 API 的文本分类和计分的准确度和细节方面有了显著改进。 使用 [API 测试控制台](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)试用。

> [!NOTE]
> * 情绪分析 v3 的请求格式和[数据限制](../overview.md#data-limits)与上一版本相同。
> * 目前，情绪分析 v3：
>    * 当前支持英语 (`en`)、日语 (`ja`)、简体中文 (`zh-Hans`)、繁体中文 (`zh-Hant`)、法语 (`fr`)、意大利语 (`it`)、西班牙语 (`es`)、荷兰语 (`nl`)、葡萄牙语 (`pt`) 和德语 (`de`)。
>    * 可供以下区域使用：`Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe` 和 `West US 2`。

|Feature |说明  |
|---------|---------|
|准确度改进     | 与以前的版本相比，文本文档在检测积极、中立、消极和复杂情绪方面有显著改进。           |
|文档和句子级别的情绪评分     | 检测文档及其中单个句子的情绪。 如果文档包含多个句子，则会为每个句子分配一个情绪分数。         |
|情绪标签和评分     | API 现在返回文本的情绪类别和情绪评分。 类别为 `positive`、`negative`、`neutral` 和 `mixed`。       |
| 输出改进 | 情绪分析现在会返回整个文本文档及其中单个句子的信息。 |
| 模型版本参数 | 一个可选参数，用于选择对数据使用的文本分析模型版本。 |

### <a name="sentiment-labeling"></a>情绪标记

情绪分析 v3 可以在句子和文档级别返回评分和标签。 评分和标签为 `positive`、`negative` 和 `neutral`。 在文档级别，也可返回 `mixed` 情绪标签（不是评分）。 可以通过聚合句子的评分来确定文档的情绪。

| 句子情绪                                                        | 返回的文档标签 |
|---------------------------------------------------------------------------|----------------|
| 至少有一个积极的句子，其余句子为中立句子。 | `positive`     |
| 至少有一个消极的句子，其余句子为中立句子。  | `negative`     |
| 至少有一个消极的句子，至少有一个积极的句子。         | `mixed`        |
| 所有句子均为中立句子。                                                 | `neutral`      |

### <a name="model-versioning"></a>模型版本控制

> [!NOTE]
> 从版本 `v3.0-preview.1` 开始，提供用于情感分析的模型版本控制。

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="sentiment-analysis-v3-example-request"></a>情绪分析 v3 示例请求

以下 JSON 是一个示例，演示了如何向新版情绪分析发出请求。 请求格式设置与上一版相同：

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

### <a name="sentiment-analysis-v3-example-response"></a>情绪分析 v3 示例响应

虽然请求格式与上一版相同，但是响应格式已更改。 以下 JSON 是一个来自新版 API 的示例响应：

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

### <a name="example-c-code"></a>示例 C# 代码

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs) 上查找一个可调用此版情绪分析的示例 C# 应用程序。

## <a name="preparation"></a>准备工作

当为情绪分析提供较小的文本块时，会得到更高质量的结果。 这与关键短语提取相反，关键短语提取在处理较大的文本块时表现更好。 要从两个操作获取最佳结果，请考虑相应地重建输入。

必须拥有以下格式的 JSON 文档：ID、文本和语言

每个文档的大小必须少于 5,120 个字符， 每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 以下示例是可能提交用于情绪分析的内容示例：

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步骤 1：构造请求

有关请求定义的详细信息，请参阅[调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 POST 请求。 若要查看此请求的 API 文档，请参阅[情绪分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)。

+ 使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTP 终结点，以便进行情绪分析。 必须在 URL 中包括 `/text/analytics/v2.1/sentiment`。 例如：`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`。

+ 设置请求头以包含文本分析操作的[访问密钥](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开[文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)中的 API 测试控制台来构造请求并将其 POST 到该服务  。

## <a name="step-2-post-the-request"></a>步骤 2：发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

记住，该服务是无状态服务。 帐户中未存储任何数据。 结果会立即在响应中返回。


## <a name="step-3-view-the-results"></a>步骤 3：查看结果

情绪分析器将文本分类为积极为主或消极为主， 并分配范围在 0 到 1 之间的分数。 接近 0.5 的值表示中性或不确定。 得分 0.5 表示中性。 如果无法分析字符串的情绪或不含情绪，则分数始终为 0.5。 例如，如果传入带有英语语言代码的西班牙语字符串，则分数为 0.5。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中。 然后，将输出导入到可以用来对数据进行排序、搜索和操作的应用程序。

下方示例展示了本文中文档集合的响应：

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="summary"></a>摘要

本文介绍了使用 Azure 认知服务中的文本分析进行情绪分析的概念和工作流。 综上所述：

+ [情绪分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) 适用于选定的语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/sentiment` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
+ 响应输出包含每个文档 ID 的情绪得分，可流式传输到接受 JSON 的任何应用。 示例应用包括 Excel 和 Power BI（仅举几例）。

## <a name="see-also"></a>另请参阅

 [文本分析概述](../overview.md)[常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [提取关键短语](text-analytics-how-to-keyword-extraction.md)
