---
title: 结合使用实体识别和文本分析 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用文本分析 REST API 识别和消除文本中找到的实体的标识。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326626"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文本分析中使用命名实体识别

[命名实体识别 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) 需要使用非结构化文本，并对每个 JSON 文档返回已消除歧义的实体列表，其中包含网上（维基百科和必应）详细信息的链接。

## <a name="entity-linking-and-named-entity-recognition"></a>实体链接和命名实体识别

文本分析的 `entities` 终结点支持命名实体识别 (NER) 和实体链接。

### <a name="entity-linking"></a>实体链接
实体链接是一种对文本中找到的实体的身份进行识别和消歧的功能（例如，确定“Mars”是用作行星还是用作罗马战神）。 此过程需要用到已识别实体链接到的知识库 — Wikipedia 用作 `entities` 终结点文本分析的知识库。

### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)
命名实体识别（NER）是一种能够识别文本中的不同实体并将它们分类为预定义的类或类型的能力。 

## <a name="named-entity-recognition-v3-public-preview"></a>命名实体识别 v3 公共预览版

下一版本的命名实体识别现可用于公共预览。 它提供实体链接和命名实体识别的更新。 尝试使用[API 测试控制台](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)。

:::row:::
    :::column span="":::
        **功能**
    :::column-end:::
    ::: column span="":::
        **说明** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        扩展的实体类型和子类型
    :::column-end:::
    :::column span="":::
     扩展了多个命名实体类型的分类和检测。
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        单独的请求终结点 
    :::column-end:::
    :::column span="":::
        用于发送实体链接和 NER 请求的不同终结点。
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` 参数
    :::column-end:::
    :::column span="":::
        用于选择文本分析模型版本的可选参数。 目前只有默认模型可供使用。
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>实体类型

命名实体识别 v3 提供跨多种类型的扩展检测。 目前，NER v3 可以识别以下类别的实体。 有关支持的实体和语言的详细列表，请参阅[命名实体类型](../named-entity-types.md)一文。

* 一般信息
* 个人信息 

### <a name="request-endpoints"></a>请求终结点

命名实体识别 v3 对 NER 和实体链接请求使用不同的终结点。 根据你的请求使用下面的 URL 格式：

NER
* 常规实体-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 个人信息实体-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>模型版本控制

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>命名实体识别 v2 支持的类型

> [!NOTE]
> 命名实体识别（NER）版本2支持以下实体。 [NER v3](#named-entity-recognition-v3-public-preview)为公共预览版，并大大扩展了文本中识别的实体数量和深度。   

| 类型  | SubType | 示例 |
|:-----------   |:------------- |:---------|
| 人员        | 暂无\*         | “Jeff”、“Bill Gates”     |
| 位置      | 暂无\*         | “Redmond, Washington”、“Paris”  |
| 组织  | 暂无\*         | “Microsoft”   |
| 数量      | Number        | “6”、“six”     |
| 数量      | 百分比    | “50%”、“fifty percent”|
| 数量      | Ordinal       | “2nd”、“second”     |
| 数量      | Age           | “90 day old”、“30 years old”    |
| 数量      | 货币      | “$10.99”     |
| 数量      | 维度     | “10 miles”、“40 cm”     |
| 数量      | 温度   | “32 degrees”    |
| DateTime      | 暂无\*         | “6:30PM February 4, 2012”      |
| DateTime      | Date          | “May 2nd, 2017”、“05/02/2017”   |
| DateTime      | 时间          | “8am”、“8:00”  |
| DateTime      | DateRange     | “May 2nd to May 5th”    |
| DateTime      | TimeRange     | “6pm to 7pm”     |
| DateTime      | 持续时间      | “1 minute and 45 seconds”   |
| DateTime      | 设置           | “every Tuesday”     |
| URL           | 暂无\*         | "https:\//www.bing.com"    |
| Email         | 暂无\*         | "support@contoso.com" |
| 美国电话号码  | 暂无\*         | （仅限美国电话号码）"（312） 555-0176" |
| IP 地址    | 暂无\*         | 10.0.0.100 |

\*一些实体可能会省略 `SubType`，具体视输入和已提取的实体而定。  列出的所有支持的实体类型仅适用于英语、简体中文、法语、德语和西班牙语。

### <a name="language-support"></a>语言支持

使用各种语言的实体链接需要使用每种语言的相应知识库。 对于文本分析中的实体链接，这意味着 `entities` 终结点支持的每种语言都将链接到该语言的相应 Wikipedia 语料库。 由于语料库的大小因语言而异，因此，实体链接功能的召回率应该也有所不同。 有关详细信息，请参阅[语言支持](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)一文。

## <a name="preparation"></a>准备工作

你必须具有以下格式的 JSON 文档： ID、文本、语言

有关当前支持的语言，请参阅[此列表](../text-analytics-supported-languages.md)。

每个文档的大小必须少于 5,120 个字符，每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 以下示例例举了可能提交至实体链接端的内容。

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步骤 1：构造请求

有关请求定义的详细信息，请参阅[如何调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 POST 请求。 查看此请求的 API 文档：[实体 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ 使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTP 终结点，以便提取关键短语。 必须包含 `/text/analytics/v2.1/entities`。 例如：`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`。

+ 设置请求标头以包括文本分析操作[的访问密钥](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开**文档**中的“API 测试控制台”来构造请求并将其 POST 到该服务[](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)。

## <a name="step-2-post-the-request"></a>步骤 2：发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

记住，该服务是无状态服务。 帐户中未存储任何数据。 结果会立即在响应中返回。

## <a name="step-3-view-results"></a>步骤 3：查看结果

所有 POST 请求都将返回 JSON 格式的响应，其中包含 ID 和检测到的属性。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。

下面展示了实体链接的输出示例：

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Summary

在本文中，你已了解使用认知服务中的文本分析进行实体链接的概念和工作流。 综上所述：

+ [实体 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) 适用于选定语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/entities` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
+ 响应输出由链接实体（包括每个文档 ID 的置信度分数、偏移量和 Web 链接）组成，可用于任何应用程序

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文本分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [文本分析概述](../overview.md)
* [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
* [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712)
