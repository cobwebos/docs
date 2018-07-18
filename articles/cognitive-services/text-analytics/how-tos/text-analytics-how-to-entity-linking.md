---
title: 如何使用文本分析 REST API（Azure 上的 Microsoft 认知服务）中的实体链接 | Microsoft Docs
description: 在本演练教程中，了解如何使用 Azure 上 Microsoft 认知服务中的文本分析 REST API 识别和解析实体。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: 55bec1a0223b70749a97a30e2da92ef15128038c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365838"
---
# <a name="how-to-identify-linked-entities-in-text-analytics-preview"></a>如何在文本分析（预览版）中识别链接实体

[实体链接 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) 采用非结构化文本，并对每个 JSON 文档返回一个已消歧实体列表，其中包含指向 Web（Wikipedia 和必应）上的详细信息的链接。 

## <a name="entity-linking-vs-named-entity-recognition"></a>实体链接与命名实体识别

在自然语言处理中，实体链接和命名实体识别 (NER) 的概念很容易混淆。 文本分析的 `entities` 终结点的预览版仅支持实体链接。

实体链接是一种对文本中找到的实体的身份进行识别和消歧的功能（例如，确定“Mars”是用作行星还是用作罗马战神）。 此过程需要用到已识别实体链接到的知识库 — Wikipedia 用作 `entities` 终结点文本分析的知识库。

### <a name="language-support"></a>语言支持

使用各种语言的实体链接需要使用每种语言的相应知识库。 对于文本分析中的实体链接，这意味着 `entities` 终结点支持的每种语言都将链接到该语言的相应 Wikipedia 语料库。 由于语料库的大小因语言而异，因此，实体链接功能的召回率应该也有所不同。


## <a name="preparation"></a>准备工作

你必须拥有以下格式的 JSON 文档：ID、文本、语言

有关当前支持的语言，请参阅[此列表](../text-analytics-supported-languages.md)。

每个文档的大小必须少于 5,000 个字符，每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 以下示例例举了可能提交至实体链接端的内容。

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                },
               {"id": "2",
                "language": "en",
                "text": "The Seattle Seahawks won the Super Bowl in 2014."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>步骤 1：构造请求

有关请求定义的详细信息，请参阅[如何调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 **POST** 请求。 请查看此请求的 API 文档：[实体链接 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)

+ 设置关键短语提取的 HTTP 终结点。 它必须包含 `/entities` 资源：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/entities`

+ 设置请求头以包含文本分析操作的访问密钥。 有关详细信息，请参阅[如何查找终结点和访问密钥](text-analytics-how-to-access-key.md)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开[文档](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)中的 **API 测试控制台**来构造请求并将其 POST 到该服务。

## <a name="step-2-post-the-request"></a>步骤 2：发布请求

在收到请求时执行分析。 该服务每分钟最多接受 100 个请求。 每个请求最大为 1 MB。

记住，该服务是无状态服务。 你的帐户中未存储任何数据。 结果会立即在响应中返回。

## <a name="step-3-view-results"></a>步骤 3：查看结果

所有 POST 请求都将返回 JSON 格式的响应，其中包含 ID 和检测到的属性。

系统会立即返回输出。 你可以将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。

下面展示了实体链接的输出示例：

```
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

响应（如果可用）包括每个检测到的实体的 Wikipedia ID、Wikipedia URL 和必应 ID。 这些可用于通过链接实体相关信息进一步增强应用程序。


## <a name="summary"></a>摘要

在本文中，你已了解使用认知服务中的文本分析进行实体链接的概念和工作流。 综上所述：

+ [实体链接 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) 适用于选定语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/entities` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](text-analytics-how-to-access-key.md)。
+ 响应输出由链接实体（包括每个文档 ID 的置信度分数、偏移量和 Web 链接）组成，可用于任何应用程序

## <a name="see-also"></a>另请参阅 

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文本分析 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
