---
title: 将元数据与 GenerateAnswer API 配合使用 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过 QnA Maker，可将键值对形式的元数据添加到问/答集。 可以筛选与用户查询的结果并存储后续会话中可用的附加信息。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/30/2019
ms.author: tulasim
ms.openlocfilehash: b18d47b4b09c6fa9c4d5f0ef87d7ebe73f151c60
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693244"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>获取与 GenerateAnswer API 和元数据的答案

若要获取用户的问题的预测的答案，请使用 GenerateAnswer API。 当您发布某一知识库时，可以看到有关如何在使用此 API 的信息**发布**页。 此外可以将 API 配置为筛选器基于元数据标记和测试知识库从具有测试查询字符串参数的终结点。

QnA Maker，可以将元数据，键和值对的形式添加到你的问题和答案的集。 结果筛选为用户查询，并将其存储在后续会话中可用的其他信息，然后可以使用此信息。 有关详细信息，请参阅[知识库](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>存储问题和答案与 QnA 实体

请务必了解 QnA Maker 如何存储的问题和答案的数据。 下图显示了一个 QnA 实体：

![QnA 实体的图示](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每个 QnA 实体都有一个唯一的永久 ID。 可以使用 ID 来更新特定的 QnA 实体。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>获取具有 GenerateAnswer API 的答案预测

使用智能机器人应用程序或应用程序中的 GenerateAnswer API 来查询您的用户问题的知识库，以获取最佳匹配项从问题和答案设置。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>发布来获取 GenerateAnswer 终结点 

发布知识库，从后[QnA Maker 门户](https://www.qnamaker.ai)，或使用[API](https://go.microsoft.com/fwlink/?linkid=2092179)，可以获取 GenerateAnswer 终结点的详细信息。

获取终结点详细信息：
1. 登录到 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在中**我知识库**，选择**查看代码**为您的知识库。
    ![我的屏幕截图的知识库](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 获取 GenerateAnswer 终结点的详细信息。

    ![终结点详细信息的屏幕截图](../media/qnamaker-how-to-metadata-usage/view-code.png)

还可以在知识库的“设置”选项卡中获取终结点详细信息  。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 请求配置

通过 HTTP POST 请求调用 GenerateAnswer。 有关演示如何调用 GenerateAnswer 的示例代码，请参阅[快速入门](../quickstarts/csharp.md)。

**请求 URL**采用以下格式： 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|HTTP 请求属性|名称|Type|目的|
|--|--|--|--|
|URL 路由参数|知识库 ID|字符串|知识库的 GUID。|
|URL 路由参数|QnAMaker 终结点主机|string|部署在 Azure 订阅中的终结点的主机名。 这是可在上找到**设置**页后发布该知识库。 |
|Header|Content-Type|string|发送到 API 的正文的媒体类型。 默认值是: '|
|Header|授权|string|终结点密钥 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|POST 正文|JSON 对象|JSON|使用设置问题。|


JSON 正文具有多个设置：

|JSON 正文属性|必选|Type|目的|
|--|--|--|--|
|`question`|必填|字符串|若要发送到您的知识库用户提出的问题。|
|`top`|可选|integer|要包含在输出中的排序结果数。 默认值为 1。|
|`userId`|可选|string|用于标识用户的唯一 ID。 此 ID 将记录在聊天日志中。|
|`scoreThreshold`|可选|integer|将返回仅与置信度高于此阈值的答案。 默认值为 0。|
|`isTest`|可选|Boolean|如果设置为 true，返回结果`testkb`搜索索引，而不是已发布的索引。|
|`strictFilters`|可选|字符串|若指定此参数，将指示 QnA Maker 仅返回含有指定元数据的答案。 使用`none`以指示响应应具有任何元数据筛选器。 |
|`RankerType`|可选|字符串|如果指定为`QuestionOnly`，告知 QnA Maker 搜索仅问题。 如果未指定，QnA Maker 搜索问题和解答。

一个 JSON 正文示例如下所示：

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
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
|Id|分配给答案的唯一 ID。|
|问题|用户提供的问题。|
|回应|问题的答案。|
|source|从中提取答案或将其存储在知识库中的源名称。|
|metadata|与答案关联的元数据。|
|metadata.name|元数据名称。 （字符串，最大长度：100，必填）|
|metadata.value|元数据值。 （字符串，最大长度：100，必填）|


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

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用元数据来按自定义元数据标记筛选答案

添加元数据可以按这些元数据标记筛选答案。 添加的元数据列**视图选项**菜单。 通过选择元数据将元数据添加到您的知识库 **+** 图标以添加的元数据对。 此对组成一个键和一个值。

![添加元数据的屏幕截图](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用元数据标记的 strictFilters 来筛选结果

请考虑用户提出的问题"当？ 此酒店关闭"，其中意图隐式的餐馆"乐园。"

因为只需要餐馆"乐园"的结果，您可以对元数据"餐馆 Name"GenerateAnswer 调用中设置筛选器。 以下示例演示了此：

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

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用问题和答案的结果以保持会话上下文

对 GenerateAnswer 的响应包含匹配的问题和答案集的相应元数据信息。 可以在客户端应用程序中使用此信息存储在更高版本的对话中使用的上一个会话的上下文。 

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

## <a name="match-questions-only-by-text"></a>按文本匹配问题，

默认情况下，QnA Maker 搜索问题和解答。 如果你想要通过问题仅搜索，若要生成答案，使用`RankerType=QuestionOnly`GenerateAnswer 请求的 POST 正文中。

您可以搜索发布的知识库，通过使用`isTest=false`，或在测试 kb 使用`isTest=true`。

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>后续步骤

**发布**页还提供的信息来生成与答案[Postman](../Quickstarts/get-answer-from-kb-using-postman.md)并[cURL](../Quickstarts/get-answer-from-kb-using-curl.md)。 

> [!div class="nextstepaction"]
> [创建知识库](./create-knowledge-base.md)
