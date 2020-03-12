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
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 0622aca5579c64c6d840761abb151665af559eea
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117478"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文本分析中使用命名实体识别

文本分析 API 允许你使用非结构化文本并返回消除歧义实体的列表，并提供指向 web 上详细信息的链接。 API 支持命名实体识别（NER）和实体链接。

### <a name="entity-linking"></a>实体链接

实体链接是指识别并消除文本中发现的实体标识的能力（例如，确定单词的出现 `Mars` 指的是指的是地球，还是罗马字的上帝）。 此过程要求使用适当的语言显示某个知识库，以便链接文本中识别的实体。 实体链接使用[维基百科](https://www.wikipedia.org/)作为此知识库。


### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

命名实体识别（NER）是一种能够识别文本中的不同实体并将它们分类为预定义的类或类型（如： person、location、event、product 和 organization）的功能。  

从版本3开始，文本分析 API 的此功能还可以标识个人和敏感信息类型，例如：电话号码、社会安全号码、电子邮件地址和银行帐号。  确定这些实体有助于对敏感文档进行分类，并编校个人信息。

## <a name="named-entity-recognition-versions-and-features"></a>命名实体识别版本和功能

文本分析 API 提供了两个版本的命名实体识别-v2 和 v3。 版本3（公共预览版）在可检测和分类的实体中提供了更多详细信息。

| Feature                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| 用于单个请求和批量请求的方法                          | X      | X      |
| 跨多个类别的基本实体识别              | X      | X      |
| 已识别实体的扩展分类                 |        | X      |
| 用于发送实体链接和 NER 请求的不同终结点。 |        | X      |
| 模型版本控制                                                |        | X      |

有关信息，请参阅[语言支持](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)。


#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

### <a name="entity-types"></a>实体类型

命名实体识别 v3 提供跨多种类型的扩展检测。 目前，NER v3 可以识别以下类别的实体：

* 常规
* 个人信息 

有关支持的实体和语言的详细列表，请参阅[NER v3 支持的实体类型](../named-entity-types.md)一文。

### <a name="request-endpoints"></a>请求终结点

命名实体识别 v3 对 NER 和实体链接请求使用不同的终结点。 根据你的请求使用下面的 URL 格式：

NER
* 常规实体-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 个人信息-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>模型版本控制

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

### <a name="entity-types"></a>实体类型

> [!NOTE]
> 命名实体识别（NER）版本2仅支持以下实体。 NER v3 为公共预览版，并大大扩展了文本中识别的实体数量和深度。   

| 类型  | 子类型 | 示例 |
|:-----------   |:------------- |:---------|
| 人员        | 暂无\*         | “Jeff”、“Bill Gates”     |
| 位置      | 暂无\*         | “Redmond, Washington”、“Paris”  |
| 组织  | 暂无\*         | “Microsoft”   |
| 数量      | Number        | “6”、“six”     |
| 数量      | 百分比    | “50%”、“fifty percent”|
| 数量      | Ordinal       | “2nd”、“second”     |
| 数量      | 年龄           | “90 day old”、“30 years old”    |
| 数量      | 货币      | “$10.99”     |
| 数量      | 维度     | “10 miles”、“40 cm”     |
| 数量      | 温度   | “32 degrees”    |
| DateTime      | 暂无\*         | “6:30PM February 4, 2012”      |
| DateTime      | Date          | “May 2nd, 2017”、“05/02/2017”   |
| DateTime      | 时间          | “8am”、“8:00”  |
| DateTime      | DateRange     | “May 2nd to May 5th”    |
| DateTime      | TimeRange     | “6pm to 7pm”     |
| DateTime      | Duration      | “1 minute and 45 seconds”   |
| DateTime      | Set           | “every Tuesday”     |
| 代码           | 暂无\*         | "https：\//www.bing.com"    |
| 电子邮件         | 暂无\*         | "support@contoso.com" |
| 美国电话号码  | 暂无\*         | （仅限美国电话号码）"（312） 555-0176" |
| IP 地址    | 暂无\*         | 10.0.0.100 |

\*一些实体可能会省略 `SubType`，具体视输入和已提取的实体而定。  列出的所有支持的实体类型仅适用于英语、简体中文、法语、德语和西班牙语。

### <a name="request-endpoints"></a>请求终结点

命名实体识别 v2 使用单个终结点进行 NER 和实体链接请求：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>发送 REST API 请求

### <a name="preparation"></a>准备工作

你必须具有以下格式的 JSON 文档： ID、文本、语言。

每个文档的长度必须为5120个字符，每个集合最多可包含1000个项（Id）。 集合在请求正文中提交。

### <a name="structure-the-request"></a>构造请求

创建 POST 请求。 可以使用以下链接中的[Postman](text-analytics-how-to-call-api.md)或**API 测试控制台**快速构建并发送一个。 

> [!NOTE]
> 可以在 Azure 门户上找到文本分析资源的密钥和终结点。 它们将位于资源的“快速启动”页上的“资源管理”下。 

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

[命名实体识别 v3 参考](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

版本3对 NER 和实体链接请求使用单独的终结点。 根据你的请求使用下面的 URL 格式：

NER
* 常规实体-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 个人信息实体-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

[命名实体识别（NER） v2 引用](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

版本2对实体链接和 NER 请求使用以下终结点： 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

发送请求标头以包括文本分析 API 密钥。 在请求正文中，提供已准备的 JSON 文档。

### <a name="example-ner-request"></a>NER 请求示例 

下面是可能发送到 API 的内容示例。 两个版本的 API 的请求格式相同。

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

文本分析 API 是无状态的。 不会在帐户中存储数据，结果会立即在响应中返回。

## <a name="view-results"></a>查看结果

所有 POST 请求都返回带有 Id 和检测到的实体属性的 JSON 格式的响应。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。


#### <a name="version-30-preview"></a>[版本 3.0-预览版）](#tab/version-3)

### <a name="example-v3-responses"></a>V3 响应示例

版本3为 NER 和实体链接提供单独的终结点。 这两个操作的响应如下所示。

#### <a name="example-ner-response"></a>NER 响应示例

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>实体链接响应示例

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>示例 NER v2 响应
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>总结

在本文中，你已了解使用认知服务中的文本分析进行实体链接的概念和工作流。 综上所述：

* 命名实体识别适用于两个版本中所选的语言。
* 请求正文中的 JSON 文档包括 ID、文本和语言代码。
* POST 请求发送到一个或多个终结点，并使用个性化[访问密钥和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)对订阅有效的终结点。
* 响应输出由链接实体（包括每个文档 ID 的置信度分数、偏移量和 Web 链接）组成，可用于任何应用程序

## <a name="next-steps"></a>后续步骤

* [文本分析概述](../overview.md)
* [使用文本分析客户端库](../quickstarts/text-analytics-sdk.md)
* [新增功能](../whats-new.md)
