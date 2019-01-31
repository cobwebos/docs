---
title: 调用文本分析 API
titlesuffix: Azure Cognitive Services
description: 了解如何调用文本分析 REST API。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 579040c3a1466d431a9ae2105edbf02fa41570b6
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211579"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>如何调用文本分析 REST API

对文本分析 API 的调用为 HTTP POST/GET 调用，可以用任何语言表示。 在本文中，我们将使用 REST 和 [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 来演示这些关键概念。

每个请求必须包括访问密钥和 HTTP 终结点。 终结点指定你在注册期间选择的区域、服务 URL 和在请求上使用的资源：`sentiment`、`keyphrases`、`languages` 和 `entities`。 

回想一下，文本分析是无状态的，因此，没有任何要管理的数据资产。 收到文本后将对其进行上传和分析，其结果会立即返回到调用应用程序。

> [!Tip]
> 对于一次性调用以了解 API 的工作原理，可以从内置 API 测试控制台发送 POST 请求，可在任何 [API 文档页](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)上使用。 没有任何设置，且唯一要求是将访问密钥和 JSON 文档粘贴到该请求。 

## <a name="prerequisites"></a>先决条件

必须拥有包含**文本分析 API** 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 

注册认知服务时，必须具有为你生成的[终结点和访问密钥](text-analytics-how-to-access-key.md)。 

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON 架构定义

输入必须是原始非结构化文本的 JSON。 不支持 XML。 架构十分简单，包括以下列表中描述的元素。 

当前可以提交所有文本分析操作的相同文档：情绪、关键短语、语言检测和实体标识。 （每个分析的架构在未来可能会有所变化。）

| 元素 | 有效值 | 必需？ | 使用情况 |
|---------|--------------|-----------|-------|
|`id` |数据类型为字符串，但实际上文档 ID 往往是整数。 | 必选 | 系统使用你提供的 ID 来构建输出。 为请求中的每个 ID 生成语言代码、关键短语和情绪分数。|
|`text` | 非结构化原始文本，最多 5,000 个字符。 | 必选 | 对于语言检测，可以使用任何语言来表示文本。 对于情绪分析、关键短语提取和实体标识，此文本必须使用[支持的语言](../text-analytics-supported-languages.md)。 |
|`language` | [支持语言](../text-analytics-supported-languages.md)的 2 字符 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 代码 | 多种多样 | 需要情绪分析、关键短语提取、实体链接；语言检测为可选。 排除语言检测不会有任何错误，但没有它会削弱分析。 语言代码应对应你提供的 `text`。 |

有关限制的详细信息，请参阅[文本分析概述 > 数据限制](../overview.md#data-limits)。 

## <a name="set-up-a-request-in-postman"></a>在 Postman 中设置请求

该服务接受最大 1 MB 的请求。 如果使用 Postman（或另一个 Web API 测试工具），则设置终结点来包含要使用的资源，并在请求标头中提供访问密钥。 每个操作均要求将适当的资源追加到终结点。 

1. 在 Postman 中：

   + 选择“Post”作为请求类型。
   + 粘贴从门户页中复制的终结点。
   + 附加资源。

  资源终结点如下所示（你的区域可能有所不同）：

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

2. 设置三个请求标头：

   + `Ocp-Apim-Subscription-Key`：从 Azure 门户获得的访问密钥。
   + `Content-Type`：application/json。
   + `Accept`：application/json。

  请求应类似于以下屏幕截图，假设 /keyPhrases 资源。

   ![请求带终结点和标头的屏幕截图](../media/postman-request-keyphrase-1.png)

4. 单击“正文”，然后选择“原始”格式。

   ![请求具有正文设置的屏幕截图](../media/postman-request-body-raw.png)

5. 采用对预期分析有效的格式粘贴某些 JSON 文档。 有关特定分析的详细信息，请参阅下面的主题：

  + [语言检测](text-analytics-how-to-language-detection.md)  
  + [关键短语提取](text-analytics-how-to-keyword-extraction.md)  
  + [情绪分析](text-analytics-how-to-sentiment-analysis.md)  
  + [实体识别（预览版）](text-analytics-how-to-entity-linking.md)  


6. 单击“发送”以提交请求。 每分钟可以提交多达 100 个请求。 

  在 Postman 中，响应会在下一个窗口中显示为单个 JSON 文档，请求中提供的每个文档 ID 对应一个条目。

## <a name="see-also"></a>另请参阅 

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [检测语言](text-analytics-how-to-language-detection.md)
