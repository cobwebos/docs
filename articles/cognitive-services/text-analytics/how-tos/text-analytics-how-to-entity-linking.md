---
title: 结合使用实体识别和文本分析 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用文本分析 REST API 识别和区分文本中找到的实体的标识。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 10/09/2020
ms.author: aahi
ms.openlocfilehash: f37828d5561ef382f572c1fdd4917a71f8a00407
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930163"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文本分析中使用命名实体识别

文本分析 API 允许你采用非结构化文本，并会返回一个已消歧实体的列表，其中包含指向 Web 上的详细信息的链接。 此 API 支持命名实体识别 (NER) 和实体链接。

### <a name="entity-linking"></a>实体链接

实体链接是指识别并消除文本 (中找到的实体标识的能力，例如，确定单词 "Mars" 的出现是指行星，还是) 的罗马上帝。 此过程要求知识库采用适当的语言，以便链接文本中识别的实体。 实体链接使用 [维基百科](https://www.wikipedia.org/) 作为此知识库。


### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

命名实体识别 (NER) 是指识别文本中不同实体，并将它们分入预定义类或类型（例如：人员、位置、事件、产品和组织）的能力。  

## <a name="named-entity-recognition-versions-and-features"></a>命名实体识别版本和功能

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Feature                                                         | NER 3。0 | NER 3.1-预览版2 |
|-----------------------------------------------------------------|--------|----------|
| 用于单个请求和批量请求的方法                          | X      | X        |
| 跨多个类别展开实体识别           | X      | X        |
| 用于发送实体链接和 NER 请求的不同终结点。 | X      | X        |
| ) 信息实体的个人 (`PII`) 和运行状况 (识别 `PHI`        |        | X        |

有关信息，请参阅 [语言支持](../language-support.md) 。

### <a name="entity-types"></a>实体类型

命名实体识别 v3 提供跨多种类型的扩展检测。 目前，NER 3.0 可以识别 " [常规实体" 类别](../named-entity-types.md)中的实体。

命名实体识别-3.1-preview。2包括了3.0 的检测功能，以及 `PII` 使用终结点检测) 个人信息 (功能 `v3.1-preview.2/entities/recognition/pii` 。 可以使用可选 `domain=phi` 参数来检测 () 的机密健康信息 `PHI` 。 有关详细信息，请参阅下面的 [实体类别](../named-entity-types.md) 文章和 [请求终结点](#request-endpoints) 部分。


## <a name="sending-a-rest-api-request"></a>发送 REST API 请求

### <a name="preparation"></a>准备工作

必须拥有以下格式的 JSON 文档：ID、文本、语言。

每个文档必须少于 5,120 个字符，每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。

### <a name="structure-the-request"></a>构造请求

创建 POST 请求。 可[使用 Postman](text-analytics-how-to-call-api.md) 或以下链接中的“API 测试控制台”来快速构建并发送请求  。 

> [!NOTE]
> 可以在 Azure 门户上找到文本分析资源的密钥和终结点。 它们将位于资源的“快速启动”页上的“资源管理”下。 


### <a name="request-endpoints"></a>请求终结点

#### <a name="version-31-preview2"></a>[版本 3.1-预览版2](#tab/version-3-preview)

命名实体识别 `v3.1-preview.2` 对于 NER 和实体链接请求使用不同的终结点。 根据你的请求使用下面的 URL 格式：

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/linking`

[命名实体识别版本 3.1-预览版引用 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)

NER
* 常规实体- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/general`

[命名实体识别版本 3.1-预览版引用 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral)

* 个人 (`PII`) 信息- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii`

你还可以使用可选 `domain=phi` 参数来检测 `PHI` 文本中) 信息的运行状况 (。 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi`

请注意，在 `redactedText` 响应 JSON 中添加属性，其中包含已修改的输入文本，检测到的 PII 实体将由每个字符的实体替换为 *。

[命名实体识别版本 3.1-预览版引用 `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)

#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

命名实体识别 v3 对 NER 和实体链接请求使用不同的终结点。 根据你的请求使用下面的 URL 格式：

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[的命名实体识别版本3.0 引用 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[的命名实体识别版本3.0 引用 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

发送请求标头以包括文本分析 API 密钥。 在请求正文中，提供已准备的 JSON 文档。

### <a name="example-ner-request"></a>NER 请求示例 

下面是可能发送到 API 的内容示例。 两个版本的 API 的请求格式相同。

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

文本分析 API 是无状态的。 不会在帐户中存储数据，结果会立即在响应中返回。

## <a name="view-results"></a>查看结果

所有 POST 请求都将返回 JSON 格式的响应，其中包含 ID 和检测到的实体属性。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。 由于多语言和表情符号支持，响应可能包含文本偏移。 有关详细信息，请参阅 [如何处理文本偏移](../concepts/text-offsets.md) 。

### <a name="example-responses"></a>示例响应

版本3为常规 NER、PII 和实体链接提供单独的终结点。 这两个操作的响应如下所示。 

#### <a name="version-31-preview"></a>[版本 3.1-preview](#tab/version-3-preview)

PII 响应示例：
```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

实体链接响应的示例：

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```


#### <a name="version-30"></a>[版本 3.0](#tab/version-3)

一般 NER 响应的示例：
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
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


## <a name="summary"></a>摘要

在本文中，你已了解使用认知服务中的文本分析进行实体链接的概念和工作流。 综上所述：

* 请求正文中的 JSON 文档包括 ID、文本和语言代码。
* 会通过对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)将 POST 请求发送到一个或多个终结点。
* 响应输出由链接实体（包括每个文档 ID 的置信度分数、偏移量和 Web 链接）组成，可用于任何应用程序

## <a name="next-steps"></a>后续步骤

* [文本分析概述](../overview.md)
* [使用文本分析客户端库](../quickstarts/text-analytics-sdk.md)
* [新增功能](../whats-new.md)
