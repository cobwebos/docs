---
title: 使用文本分析 REST API 进行关键短语提取 | Microsoft Docs
description: 如何通过 Azure 认知服务使用文本分析 REST API 提取关键短语。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 9cb6a40e6ce53a6d82d4129db876a1b24269f166
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997710"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>示例：如何使用文本分析提取关键短语

[关键短语提取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) 用于计算非结构化的文本，并针对每个 JSON 文档返回关键短语列表。 

如果需要快速确定文档集中的要点，此功能十分有用。 例如，给定输入文本“The food was delicious and there were wonderful staff”，服务会返回谈话要点：“food”和“wonderful staff”。

目前，关键短语提取支持英语、德语、西班牙语和日语。 其他语言以预览版提供。 有关详细信息，请参阅[支持的语言](../text-analytics-supported-languages.md)。

> [!TIP]
> 文本分析还提供一个基于 Linux 的 Docker 容器映像，用于提取关键短语，因此可以在靠近数据的位置[安装并运行文本分析容器](text-analytics-how-to-install-containers.md)。

## <a name="preparation"></a>准备工作

提供的要处理的文本量越大，关键短语提取效果越好。 这恰好与情绪分析（文本量越小，效果越好）相反。 要从两个操作获取最佳结果，请考虑相应地重建输入。

必须拥有以下格式的 JSON 文档：ID、文本、语言

每个文档的大小必须少于 5,120 个字符，每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 以下示例例举了可能提交以进行关键短语提取的内容。

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

有关请求定义的详细信息，请参阅[如何调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 POST 请求。 查看此请求的 API 文档：[关键短语 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)

+ 使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTP 终结点，以便提取关键短语。 它必须包含 `/keyPhrases` 资源：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`

+ 设置请求头以包含文本分析操作的访问密钥。 有关详细信息，请参阅[如何查找终结点和访问密钥](text-analytics-how-to-access-key.md)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开[文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)中的“API 测试控制台”来构造请求并将其 POST 到该服务。

## <a name="step-2-post-the-request"></a>步骤 2：发布请求

在收到请求时执行分析。 该服务每分钟最多接受 100 个请求。 每个请求最大为 1 MB。

记住，该服务是无状态服务。 帐户中未存储任何数据。 结果会立即在响应中返回。

## <a name="step-3-view-results"></a>步骤 3：查看结果

所有 POST 请求都将返回 JSON 格式的响应，其中包含 ID 和检测到的属性。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。

关键短语提取输出的示例如下所示：

```json
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

如上所述，分析器查找和放弃不重要的字词，并保留似乎是句子主语或宾语的字词或短语。 

## <a name="summary"></a>摘要

在本文中，你已了解使用认知服务中的文本分析进行关键短语提取的概念和工作流。 摘要：

+ [关键短语提取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) 适用于所选语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/keyphrases` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](text-analytics-how-to-access-key.md)。
+ 响应输出包含每个文档 ID 的关键单词和短语，可以流式传输到接受 JSON 的任何应用，包括 Excel 和 Power BI（仅举几例）。

## <a name="see-also"></a>另请参阅 

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文本分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
