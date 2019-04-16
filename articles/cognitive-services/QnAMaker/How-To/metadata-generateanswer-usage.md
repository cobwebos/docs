---
title: 将元数据与 GenerateAnswer API 配合使用 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过 QnA Maker，可将键值对形式的元数据添加到问/答集。 此信息可用于筛选用户查询的结果，以及存储可用于后续对话的其他信息。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: tulasim
ms.openlocfilehash: d14e2897183a97da5e84a76b699def529f1d167e
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579404"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>获取一个使用 GenerateAnswer API 和元数据的知识答案

若要获取用户的问题的预测的答案，请使用 GenerateAnswer API。 当您发布某一知识库时，在发布页上会显示此信息才能使用此 API。 您还可以将 API 配置为筛选器基于元数据标记和测试知识库从具有测试查询字符串参数的终结点。

通过 QnA Maker，可将键值对形式的元数据添加到问/答集。 此信息可用于筛选用户查询的结果，以及存储可用于后续对话的其他信息。 有关详细信息，请参阅[知识库](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>存储与 QnA 实体的问题和解答

首先，理解 QnA Maker 存储问/答数据的方式是非常重要的。 下图显示了一个 QnA 实体：

![QnA 实体](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每个 QnA 实体都有一个唯一的永久 ID。 ID 可用于对特定 QnA 实体进行更新。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>获取具有 GenerateAnswer API 的答案预测

使用聊天机器人或应用程序中的 GenerateAnswer API，能以某个用户问题对知识库进行查询，从问/答集中获取匹配度最高的结果。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>发布来获取 GenerateAnswer 终结点 

发布知识库后，从 [QnA Maker 门户](https://www.qnamaker.ai)或使用 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) 都可以获取 GenerateAnswer 终结点的详细信息。

获取终结点详细信息：
1. 登录至 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在“我的知识库”中，单击知识库的“查看代码”。
    ![我的知识库](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 获取 GenerateAnswer 终结点的详细信息。

    ![终结点详细信息](../media/qnamaker-how-to-metadata-usage/view-code.png)

还可以在知识库的“设置”选项卡中获取终结点详细信息。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 请求配置

通过 HTTP POST 请求调用 GenerateAnswer。 有关演示如何调用 GenerateAnswer 的示例代码，请参阅[快速入门](../quickstarts/csharp.md)。

**请求 URL**采用以下格式： 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer?isTest=true
```

|HTTP 请求属性|名称|Type|目的|
|--|--|--|--|
|URL 路由参数|知识库 ID|字符串|知识库的 GUID。|
|URL 路由参数|QnAMaker 终结点主机|字符串|部署在 Azure 订阅中的终结点的主机名。 这是可在设置页上后发布该知识库。 |
|标头|Content-Type|字符串|发送到 API 的正文的媒体类型。 默认值是: '|
|标头|授权|字符串|终结点密钥 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|POST 正文|JSON 对象|JSON|使用设置问题|


JSON 正文具有多个设置：

|JSON 正文属性|需要|Type|目的|
|--|--|--|--|
|`question`|必填|字符串|若要发送到您的知识库用户提出的问题。|
|`top`|可选|integer|要包含在输出中的排序结果数。 默认值为 1。|
|`userId`|可选|字符串|用于标识用户的唯一 ID。 此 ID 将记录在聊天日志中。|
|`isTest`|可选|布尔值|如果设置为 true，返回结果`testkb`搜索索引，而不是已发布的索引。|
|`strictFilters`|可选|字符串|若指定此参数，将指示 QnA Maker 仅返回含有指定元数据的答案。|

一个 JSON 正文示例如下所示：

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 响应属性

成功的响应返回状态 200 和 JSON 响应。 

|答案属性 （按评分排序）|目的|
|--|--|
|score|排名分数，介于 0 到 100 之间。|
|ID|分配给答案的唯一 ID。|
|问题|用户提供的问题。|
|回应|问题的答案。|
|源|从中提取答案或将其存储在知识库中的源名称。|
|metadata|与答案关联的元数据。|
|metadata.name|元数据名称。 （字符串，最大长度：100，必填）|
|metadata.value:元数据值。 （字符串，最大长度：100，必填）|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>使用元数据，可按自定义元数据标记筛选答案

添加元数据可以按这些元数据标记筛选答案。 请考虑以下常见问题解答数据。 通过单击元数据图标，将元数据添加到知识库。

![添加元数据](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用元数据标记的 strictFilters 来筛选结果

请思考此用户问题 -“此酒店什么时候打烊？” 这里隐含的意向是“Paradise”餐馆。

由于只需要关于餐馆“Paradise”的结果，因此可以在 GenerateAnswer 调用中对元数据“餐馆名称”设置一个筛选器，如下所示。

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用问题和答案的结果以保持会话上下文

对 GenerateAnswer 的响应包含匹配的问题/答案集的相应元数据信息。 此信息可以在客户端应用程序中，用于存储在更高版本的对话中使用的上一个会话的上下文。 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

发布页还提供了使用 [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) 和 [cURL](../Quickstarts/get-answer-from-kb-using-curl.md) 生成答案的信息。 

> [!div class="nextstepaction"]
> [创建知识库](./create-knowledge-base.md)
