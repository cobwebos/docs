---
title: 通过文本分析 REST API 检测语言
titleSuffix: Azure Cognitive Services
description: 通过 Azure 认知服务使用文本分析 REST API 检测语言。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 361dc10aad6f65230e1e0f4c563534d44b45f902
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697810"
---
# <a name="example-detect-language-with-text-analytics"></a>示例：通过文本分析检测语言

Azure 文本分析 REST API 的[语言检测](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)功能评估每个文档的文本输入，并返回带有指示分析强度的分数的语言标识符。

此功能对于用于收集语言未知的任意文本的内容存储非常有用。 可以解析此分析的结果，确定输入文档中使用的语言。 响应还返回一个分数，反映模型的置信度。 分数值介于 0 到 1 之间。

语言检测功能可以检测多种语言、变体、方言和某些区域或文化语言。 此功能的确切语言列表未发布。

如果内容是用较少使用的语言表示的，则可以尝试“语言检测”功能来查看它是否返回代码。 无法检测到的语言的响应为 `unknown`。

> [!TIP]
> 文本分析还提供一个基于 Linux 的 Docker 容器映像，用于检测语言，因此可以在靠近数据的位置[安装并运行文本分析容器](text-analytics-how-to-install-containers.md)。

## <a name="preparation"></a>准备工作

必须拥有以下格式的 JSON 文档：ID 和文本。

每个文档的大小必须少于 5,120 个字符， 每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 以下示例是可能提交用于语言检测的内容示例：

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步骤 1：构造请求

有关请求定义的详细信息，请参阅[调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 POST 请求。 若要查看此请求的 API 文档，请参阅[语言检测 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)。

+ 设置语言检测的 HTTP 终结点。 使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)。 它必须包含 `/languages` 资源：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`。

+ 设置请求头以包含文本分析操作的[访问密钥](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开[文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)中的“API 测试控制台”来构造请求并将其 POST 到该服务  。

## <a name="step-2-post-the-request"></a>步骤 2：POST 请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

记住，该服务是无状态服务。 帐户中未存储任何数据。 结果会立即在响应中返回。


## <a name="step-3-view-the-results"></a>步骤 3：查看结果

所有 POST 请求都将返回 JSON 格式的响应，其中包含 ID 和检测到的属性。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中。 然后，将输出导入到可以用来对数据进行排序、搜索和操作的应用程序。

示例请求的结果应类似于以下 JSON。 请注意，它是一个包含多个项的文档。 输出采用英文。 语言标识符包括友好名称和 [ISO 639-1](https://www.iso.org/standard/22109.html) 格式的语言代码。

正分 1.0 表示分析可能达到的最高可信度。

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "Spanish",
                        "iso6391Name": "es",
                        "score": 1
                    }
                ]
            },
            {
                "id": "3",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            },
            {
                "id": "4",
                "detectedLanguages": [
                    {
                        "name": "Chinese_Simplified",
                        "iso6391Name": "zh_chs",
                        "score": 1
                    }
                ]
            },
            {
                "id": "5",
                "detectedLanguages": [
                    {
                        "name": "Russian",
                        "iso6391Name": "ru",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="ambiguous-content"></a>不明确的内容

在某些情况下，可能很难根据输入区分语言。 可以使用 `countryHint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `countryHint = ""` 来重置此参数。

例如，“Impossible”对于英语和法语都是通用的，如果在有限的背景下给出，则响应将基于“美国”国家/地区提示。 如果已知文本来源来自法国，可以将其作为提示给出。

**输入**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

现在，该服务提供了其他上下文来帮助做出更好的判断： 

**输出**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

如果分析器无法分析输入，则会返回 `(Unknown)`。 例如，如果提交的文本块仅包含阿拉伯数字，则会出现这种情况。

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>混合语言内容

同一文档中的混合语言内容将返回内容中代表性最强但正评级较低的语言。 评级反映该评估的边界强度。 在以下示例中，输入是英语、西班牙语和法语的混合。 分析器对每个段中的字符进行计数，确定主要语言。

**输入**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**输出**

生成的输出包含主要语言，分数低于 1.0，表示可信度较低。

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 0.9375
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>摘要

本文介绍了使用 Azure 认知服务中的文本分析进行语言检测的概念和工作流。 其中解释并演示了以下要点：

+ [语言检测](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)可用于多种语言、变体、方言和某些区域或文化语言。
+ 请求正文中的 JSON 文档包括 ID 和文本。
+ POST 请求的目标是 `/languages` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
+ 响应输出包含每个文档 ID 的语言标识符。 输出可以流式传输到接受 JSON 的任何应用。 示例应用包括 Excel 和 Power BI（仅举几例）。

## <a name="see-also"></a>另请参阅

 [文本分析概述](../overview.md)[常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [分析情绪](text-analytics-how-to-sentiment-analysis.md)
