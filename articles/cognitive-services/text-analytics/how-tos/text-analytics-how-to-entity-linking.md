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
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203485"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文本分析中使用命名实体识别

文本分析 API 允许您获取非结构化文本，并返回消除歧义的实体列表，并链接到 Web 上的详细信息。 API 支持命名实体识别 （NER） 和实体链接。

### <a name="entity-linking"></a>实体链接

实体链接是识别和消除文本中找到实体身份的能力（例如，确定单词`Mars`的出现是指地球还是罗马战争之神）。 此过程要求以适当语言存在知识库，以在文本中链接已识别的实体。 实体链接使用[维基百科](https://www.wikipedia.org/)作为这个知识库。


### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

命名实体识别 （NER） 是识别文本中不同实体并将其分类为预定义的类或类型（如：人员、位置、事件、产品和组织）的能力。  

从第 3 版开始，文本分析 API 的此功能还可以识别个人和敏感信息类型，例如：电话号码、社会保险号、电子邮件地址和银行帐号。  识别这些实体有助于对敏感文档进行分类，并编辑个人信息。

## <a name="named-entity-recognition-versions-and-features"></a>命名实体识别版本和功能

文本分析 API 提供两个版本的命名实体识别 - v2 和 v3。 版本 3（公共预览版）提供了可检测和分类的实体中增加的详细信息。

| Feature                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| 用于单个请求和批量请求的方法                          | X      | X      |
| 跨多个类别的基本实体识别              | X      | X      |
| 已确认实体的扩展分类                 |        | X      |
| 用于发送实体链接和 NER 请求的单独终结点。 |        | X      |
| 模型版本控制                                                |        | X      |

有关信息，请参阅[语言支持](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)。


#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

### <a name="entity-types"></a>实体类型

命名实体识别 v3 提供跨多种类型的扩展检测。 目前，NER v3 可以识别以下类别的实体：

* 常规
* 个人信息 

有关支持实体和语言的详细列表，请参阅 NER [v3 支持的实体类型](../named-entity-types.md)文章。

### <a name="request-endpoints"></a>请求终结点

命名实体识别 v3 对 NER 和实体链接请求使用单独的终结点。 根据您的请求使用以下 URL 格式：

内尔
* 一般实体 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 个人信息 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>模型版本控制

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

### <a name="entity-types"></a>实体类型

> [!NOTE]
> 命名实体识别 （NER） 版本 2 仅支持以下实体。 NER v3 处于公共预览版中，并极大地扩展了文本中识别的实体的数量和深度。   

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
| DateTime      | 设置           | “every Tuesday”     |
| 代码           | 暂无\*         | "https:\//www.bing.com"    |
| 电子邮件         | 暂无\*         | "support@contoso.com" |
| 美国电话号码  | 暂无\*         | （仅限美国电话号码）"(312) 555-0176" |
| IP 地址    | 暂无\*         | "10.0.0.100" |

\*一些实体可能会省略 `SubType`，具体视输入和已提取的实体而定。  列出的所有支持的实体类型仅适用于英语、中文、法语、德语和西班牙语。

### <a name="request-endpoints"></a>请求终结点

命名实体识别 v2 使用 NER 和实体链接请求的单个终结点：

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>发送 REST API 请求

### <a name="preparation"></a>准备工作

您必须具有此格式的 JSON 文档：ID、文本、语言。

每个文档必须低于 5，120 个字符，并且每个集合最多可以有 1，000 个项 （ID）。 集合在请求正文中提交。

### <a name="structure-the-request"></a>构造请求

创建 POST 请求。 您可以在以下链接中使用[Postman](text-analytics-how-to-call-api.md)或**API 测试控制台**来快速构建和发送一个。 

> [!NOTE]
> 可以在 Azure 门户上找到文本分析资源的密钥和终结点。 它们将位于资源的“快速启动”**** 页上的“资源管理”**** 下。 

#### <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

[命名实体识别 v3 引用](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

版本 3 对 NER 和实体链接请求使用单独的终结点。 根据您的请求使用以下 URL 格式：

内尔
* 一般实体 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 个人信息实体 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[版本 2.1](#tab/version-2)

[命名实体识别 （NER） v2 引用](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

版本 2 使用以下终结点进行实体链接和 NER 请求： 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

发送请求标头以包括文本分析 API 密钥。 在请求正文中，提供您准备的 JSON 文档。

### <a name="example-ner-request"></a>示例 NER 请求 

下面是可能发送到 API 的内容的示例。 两个版本的 API 的请求格式相同。

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

所有 POST 请求都返回带有 DID 和检测到的实体属性的 JSON 格式化的响应。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。 由于多语言和表情符号支持，响应可能包含文本偏移。 [有关详细信息，请参阅如何处理文本偏移](../concepts/text-offsets.md)。

#### <a name="version-30-preview"></a>[版本 3.0 预览版）](#tab/version-3)

### <a name="example-v3-responses"></a>示例 v3 响应

版本 3 为 NER 和实体链接提供了单独的终结点。 下面列出了两个操作的响应。 

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

#### <a name="example-entity-linking-response"></a>链接响应的示例实体

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

* 命名实体识别可用于两个版本的选定语言。
* 请求正文中的 JSON 文档包括 ID、文本和语言代码。
* POST 请求使用个性化[访问密钥和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)对订阅有效的终结点发送到一个或多个终结点。
* 响应输出由链接实体（包括每个文档 ID 的置信度分数、偏移量和 Web 链接）组成，可用于任何应用程序

## <a name="next-steps"></a>后续步骤

* [文本分析概述](../overview.md)
* [使用文本分析客户端库](../quickstarts/text-analytics-sdk.md)
* [新增功能](../whats-new.md)
