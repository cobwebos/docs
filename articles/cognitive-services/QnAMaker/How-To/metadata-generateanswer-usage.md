---
title: 将元数据与 GenerateAnswer API 配合使用 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过 QnA Maker，可将键值对形式的元数据添加到问/答集。 您可以将结果筛选为用户查询，并存储可用于后续对话的其他信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220640"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>使用生成应答 API 和元数据获取答案

要获取用户问题的预测答案，请使用生成应答 API。 发布知识库时，您可以在 **"发布"** 页上看到有关如何使用此 API 的信息。 还可以配置 API 以根据元数据标记筛选答案，以及使用测试查询字符串参数从终结点测试知识库。

QnA Maker 允许您以键和值对的形式将元数据添加到一组问题和答案中。 然后，可以使用此信息将结果筛选到用户查询，并存储可用于后续对话的其他信息。 有关详细信息，请参阅[知识库](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>使用 QnA 实体存储问题和答案

了解 QnA Maker 如何存储问题和应答数据非常重要。 下图显示了一个 QnA 实体：

![QnA 实体的插图](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每个 QnA 实体都有一个唯一的永久 ID。 您可以使用 ID 对特定的 QnA 实体进行更新。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>使用生成应答 API 获取答案预测

在机器人或应用程序中使用[GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)使用用户问题查询知识库，以便从问答集中获得最佳匹配。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>发布以获取生成应答终结点

发布知识库后，无论是从[QnA Maker 门户](https://www.qnamaker.ai)，还是通过使用[API，](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)都可以获取生成应答终结点的详细信息。

获取终结点详细信息：
1. 登录到[https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. **在"我的知识库"中**，选择 **"查看知识库的代码**"。
    ![我的知识库截图](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 获取 GenerateAnswer 终结点的详细信息。

    ![终结点详细信息的屏幕截图](../media/qnamaker-how-to-metadata-usage/view-code.png)

还可以在知识库的“设置”选项卡中获取终结点详细信息****。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>生成应答请求配置

通过 HTTP POST 请求调用 GenerateAnswer。 有关演示如何调用 GenerateAnswer 的示例代码，请参阅[快速入门](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)。

POST 请求使用：

* 所需的[URI 参数](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 安全所需的标头属性`Authorization`，
* 必需[的正文属性](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)。

生成应答 URL 具有以下格式：

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

请记住，使用具有尾随空格的字符串`Authorization``EndpointKey`的值设置 的 HTTP 标头属性，然后在 **"设置"** 页上找到的终结点键。

JSON 正文的示例如下所示：

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

了解有关[rank 类型](../concepts/best-practices.md#choosing-ranker-type)的更多。

以前的 JSON 仅请求的答案为 30% 或高于阈值分数。

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>生成应答响应属性

[响应](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)是 JSON 对象，包括显示答案所需的所有信息，以及对话的下一个回合（如果可用）。

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

上一份JSON的回答是38.5%。

## <a name="use-qna-maker-with-a-bot-in-c"></a>将 QnA 制造商与 C 中的自动程序一起使用#

机器人框架提供访问QnA制造商的属性与[getAnswer API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)：

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

以前的 JSON 仅请求的答案为 30% 或高于阈值分数。

支持机器人有[一个](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418)带有此代码的示例。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>在 Node.js 中使用带有自动程序的 QnA 制造商

机器人框架提供访问QnA制造商的属性与[getAnswer API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)：

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

以前的 JSON 仅请求的答案为 30% 或高于阈值分数。

支持机器人有[一个](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36)带有此代码的示例。

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用元数据按自定义元数据标记筛选答案

通过添加元数据，您可以通过这些元数据标记筛选答案。 从 **"视图选项"** 菜单添加元数据列。 通过选择元数据**+** 图标来添加元数据对，将元数据添加到知识库中。 此对由一个键和一个值组成。

![添加元数据的屏幕截图](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用元数据标记的 strictFilters 来筛选结果

考虑用户的问题"这家酒店何时关闭？"

由于结果仅对餐厅"天堂"是必需的，因此您可以在元数据"餐厅名称"的"生成应答"调用中设置筛选器。 以下示例显示了这一点：

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用问答结果保持对话上下文

对"生成答案"的响应包含匹配的问题和答案集的相应元数据信息。 您可以在客户端应用程序中使用此信息来存储上一个对话的上下文，以便以后的对话中使用。

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

## <a name="match-questions-only-by-text"></a>仅按文本匹配问题

默认情况下，QnA Maker 会搜索问题和答案。 如果只想搜索问题，要生成答案，请使用`RankerType=QuestionOnly`"生成应答"请求的 POST 正文。

您可以使用 在 已发布的 kb`isTest=false`中搜索 、使用 或`isTest=true`使用 在 测试 kb 中搜索 。

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
|2xx|Success|
|400|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|密钥无效|
|403|禁止 - 没有正确的权限|
|404|知识库不存在|
|410|此 API 已弃用，不再可用|

## <a name="next-steps"></a>后续步骤

**"发布"** 页还提供信息，以便使用 Postman 或 cURL[生成答案](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)。

> [!div class="nextstepaction"]
> [创建知识库机器人](../tutorials/integrate-qnamaker-luis.md)
