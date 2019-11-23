---
title: 将元数据与 GenerateAnswer API 配合使用 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过 QnA Maker，可将键值对形式的元数据添加到问/答集。 You can filter results to user queries, and store additional information that can be used in follow-up conversations.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 0190b94cc6195163de4d428c2cae0de3620bdb01
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422699"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Get an answer with the GenerateAnswer API and metadata

To get the predicted answer to a user's question, use the GenerateAnswer API. When you publish a knowledge base, you can see information about how to use this API on the **Publish** page. You can also configure the API to filter answers based on metadata tags, and test the knowledge base from the endpoint with the test query string parameter.

QnA Maker lets you add metadata, in the form of key and value pairs, to your sets of questions and answers. You can then use this information to filter results to user queries, and to store additional information that can be used in follow-up conversations. 有关详细信息，请参阅[知识库](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Store questions and answers with a QnA entity

It's important to understand how QnA Maker stores the question and answer data. 下图显示了一个 QnA 实体：

![Illustration of a QnA entity](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每个 QnA 实体都有一个唯一的永久 ID。 You can use the ID to make updates to a particular QnA entity.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Get answer predictions with the GenerateAnswer API

You use the [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) in your bot or application to query your knowledge base with a user question, to get the best match from the question and answer sets.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publish to get GenerateAnswer endpoint 

After you publish your knowledge base, either from the [QnA Maker portal](https://www.qnamaker.ai), or by using the [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), you can get the details of your GenerateAnswer endpoint.

获取终结点详细信息：
1. 登录到 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. In **My knowledge bases**, select **View Code** for your knowledge base.
    ![Screenshot of My knowledge bases](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 获取 GenerateAnswer 终结点的详细信息。

    ![Screenshot of endpoint details](../media/qnamaker-how-to-metadata-usage/view-code.png)

还可以在知识库的“设置”选项卡中获取终结点详细信息。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer request configuration

通过 HTTP POST 请求调用 GenerateAnswer。 有关演示如何调用 GenerateAnswer 的示例代码，请参阅[快速入门](../quickstarts/create-publish-kb-csharp-sdk.md#generate-an-answer-from-the-knowledge-base)。 

The POST request uses:

* Required [URI parameters](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Required [header property](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, for security
* Required [body properties](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

The GenerateAnswer URL has the following format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Remember to set the HTTP header property of `Authorization` with a value of the string `EndpointKey` with a trailing space then the endpoint key found on the **Settings** page.

An example JSON body looks like:

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

Learn more about [rankerType](../concepts/best-practices.md#choosing-ranker-type).

The previous JSON requested only answers that are at 30% or above the threshold score. 

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer response properties

The [response](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) is a JSON object including all the information you need to display the answer and the next turn in the conversation, if available.

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

The previous JSON responded with an answer with a score of 38.5%. 

## <a name="use-qna-maker-with-a-bot-in-c"></a>Use QnA Maker with a bot in C#

The bot framework provides access to the QnA Maker's properties with the [getAnswer API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

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

The previous JSON requested only answers that are at 30% or above the threshold score. 

The Support bot has [an example](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) with this code.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Use QnA Maker with a bot in Node.js

The bot framework provides access to the QnA Maker's properties with the [getAnswer API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

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

The previous JSON requested only answers that are at 30% or above the threshold score. 

The Support bot has [an example](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) with this code.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Use metadata to filter answers by custom metadata tags

Adding metadata allows you to filter the answers by these metadata tags. Add the metadata column from the **View Options** menu. Add metadata to your knowledge base by selecting the metadata **+** icon to add a metadata pair. This pair consists of one key and one value.

![Screenshot of adding metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用元数据标记的 strictFilters 来筛选结果

Consider the user question "When does this hotel close?", where the intent is implied for the restaurant "Paradise."

Because results are required only for the restaurant "Paradise", you can set a filter in the GenerateAnswer call on the metadata "Restaurant Name". The following example shows this:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Use question and answer results to keep conversation context

The response to the GenerateAnswer contains the corresponding metadata information of the matched question and answer set. You can use this information in your client application to store the context of the previous conversation for use in later conversations. 

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

## <a name="match-questions-only-by-text"></a>Match questions only, by text

By default, QnA Maker searches through questions and answers. If you want to search through questions only, to generate an answer, use the `RankerType=QuestionOnly` in the POST body of the GenerateAnswer request.

You can search through the published kb, using `isTest=false`, or in the test kb using `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Common HTTP errors

|代码|说明|
|:--|--|
|2xx|Success|
|400|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|密钥无效|
|403|禁止 - 没有正确的权限|
|404|知识库不存在|
|410|此 API 已弃用，不再可用|

## <a name="next-steps"></a>后续步骤

The **Publish** page also provides information to [generate an answer](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) with Postman or cURL.

> [!div class="nextstepaction"]
> [Create a knowledge base bot](../tutorials/integrate-qnamaker-luis.md)
