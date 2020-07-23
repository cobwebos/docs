---
title: 将元数据与 GenerateAnswer API 配合使用 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 使你可以将元数据以键/值对的形式添加到问题/答案对。 你可以将结果筛选为用户查询，并存储可在后续对话中使用的其他信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: diberry
ms.openlocfilehash: 171efd0e5750555130588f783c4a858def11afec
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993501"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>使用 GenerateAnswer API 和元数据获取答案

若要获取用户问题的预测答案，请使用 GenerateAnswer API。 发布知识库时，可以在 "**发布**" 页上查看有关如何使用此 API 的信息。 你还可以配置 API 来基于元数据标记筛选答案，并通过测试查询字符串参数来测试终结点中的知识库。

QnA Maker 使你能够以键和值对的形式向你的问题和答案对添加元数据。 然后，你可以使用此信息对用户查询的结果进行筛选，以及存储可在后续对话中使用的其他信息。 有关详细信息，请参阅[知识库](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>用 QnA 实体存储问题和答案

务必了解 QnA Maker 如何存储问题和答案数据。 下图显示了一个 QnA 实体：

![QnA 实体的插图](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每个 QnA 实体都有一个唯一的永久 ID。 您可以使用该 ID 来更新特定的 QnA 实体。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>通过 GenerateAnswer API 获取答案预测

在机器人或应用程序中使用[GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)可以通过用户问题查询知识库，以便从问题和答案对中获得最佳匹配。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>发布以获取 GenerateAnswer 终结点

从[QnA Maker 门户](https://www.qnamaker.ai)或使用[API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)发布知识库后，可以获取 GenerateAnswer 终结点的详细信息。

获取终结点详细信息：
1. 登录到 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在**知识库**中，选择 "**查看**知识库" 代码。
    ![我的知识库的屏幕截图](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 获取 GenerateAnswer 终结点的详细信息。

    ![终结点详细信息的屏幕截图](../media/qnamaker-how-to-metadata-usage/view-code.png)

还可以在知识库的“设置”选项卡中获取终结点详细信息****。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 请求配置

通过 HTTP POST 请求调用 GenerateAnswer。 有关演示如何调用 GenerateAnswer 的示例代码，请参阅[快速入门](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)。

POST 请求使用：

* 必需的[URI 参数](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 必需的标头属性， `Authorization` 用于安全性
* 必需的[正文属性](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)。

GenerateAnswer URL 具有以下格式：

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

请记住，使用带有尾随空格的字符串的值设置的 HTTP 标头属性， `Authorization` `EndpointKey` 然后在 "**设置**" 页上找到终结点键。

示例 JSON 正文如下所示：

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

了解有关[rankerType](../concepts/best-practices.md#choosing-ranker-type)的详细信息。

以前的 JSON 只请求30% 或高于阈值分数的答案。

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 响应属性

[响应](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)是一个 JSON 对象，其中包括显示答案所需的所有信息，以及下一次打开会话（如果可用）。

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
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

之前的 JSON 响应，分数为38.5%。

## <a name="use-qna-maker-with-a-bot-in-c"></a>将 QnA Maker 与 C 中的机器人配合使用#

机器人框架使用[GETANSWER API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)提供对 QnA Maker 的属性的访问权限：

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

以前的 JSON 只请求30% 或高于阈值分数的答案。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>将 QnA Maker 与 node.js 中的机器人配合使用

机器人框架使用[GETANSWER API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)提供对 QnA Maker 的属性的访问权限：

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

以前的 JSON 只请求30% 或高于阈值分数的答案。

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用元数据筛选自定义元数据标记的答案

通过添加元数据，可以按这些元数据标记筛选答案。 从 "**视图选项**" 菜单添加 "元数据" 列。 通过选择元数据图标来添加元数据对，将元数据添加到知识库中 **+** 。 此对包含一个键和一个值。

![添加元数据的屏幕截图](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用元数据标记的 strictFilters 来筛选结果

请考虑用户问题 "此宾馆何时关闭？"，其中，该意向适用于餐馆 "Paradise"。

由于只有餐馆 "Paradise" 需要结果，因此可以对元数据 "餐馆 Name" 在 GenerateAnswer 调用中设置筛选器。 下面的示例演示了这一点：

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用问题和答案结果来保留会话上下文

对 GenerateAnswer 的响应包含匹配的问题和答案对的相应元数据信息。 你可以在客户端应用程序中使用此信息来存储以前会话的上下文，以便在以后的会话中使用。

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

## <a name="match-questions-only-by-text"></a>仅匹配问题，按文本

默认情况下，QnA Maker 搜索问题和答案。 如果只想要搜索问题，若要生成答案，请 `RankerType=QuestionOnly` 在 GenerateAnswer 请求的 POST 正文中使用。

可以 `isTest=false` 使用在测试 kb 中搜索已发布的 kb、使用或 `isTest=true` 。

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>常见 HTTP 错误

|代码|说明|
|:--|--|
|2xx|成功|
|400|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|密钥无效|
|403|禁止 - 没有正确的权限|
|404|知识库不存在|
|410|此 API 已弃用，不再可用|

## <a name="next-steps"></a>后续步骤

"**发布**" 页还提供了使用 Postman 或卷曲[生成答案](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)的信息。

> [!div class="nextstepaction"]
> [获取有关知识库的分析](../how-to/get-analytics-knowledge-base.md)
