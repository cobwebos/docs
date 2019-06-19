---
title: 改进知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过主动学习，可以根据用户提交的内容为问题和答案对提供替代问题建议，从而提高知识库的质量。 查看这些建议后，可以将其添加到现有问题，也可以拒绝它们。 知识库不会自动更改。 你必须接受的建议进行的任何更改才会生效。 这些建议会添加问题，但不会更改或删除现有问题。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: b73884e544ea1b8ee76c8a891048e6a8e17d6ab3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204090"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>使用 active 学习来改善您的知识库

通过主动学习，可以根据用户提交的内容为问题和答案对提供替代问题建议，从而提高知识库的质量。 查看这些建议后，可以将其添加到现有问题，也可以拒绝它们。 

知识库不会自动更改。 为了使任何更改才会生效，必须接受这些建议。 这些建议会添加问题，但不会更改或删除现有问题。

## <a name="what-is-active-learning"></a>主动学习是什么？

QnA Maker 通过隐式和显式反馈学习新的问题变体。
 
* [隐式反馈](#how-qna-makers-implicit-feedback-works)– 排名器了解用户问题具有多个答案非常接近的分数时，将其视为作为反馈。 不需要执行这种情况发生的任何操作。
* [显式反馈](#how-you-give-explicit-feedback-with-the-train-api)– 时使用小分数的变体的多个答案将返回从知识库，客户端应用程序会要求用户的问题是正确的问题。 用户的显式反馈发送到使用 QnA Maker[训练 API](#train-api)。 

这两种方法提供的群集化的类似查询排名程序。

## <a name="how-active-learning-works"></a>主动学习的工作原理

主动学习，将触发基于返回通过 QnA Maker 的前几个答案的分数。 如果分数差异位于较小范围内，查询是为每个可能的 QnA 对视为可能建议 （作为一个备用的问题）。 一旦接受特定的 QnA 对建议的问题，则会拒绝其他对。 需记住要保存并训练后正在接受建议。

在终结点获得合理数量和类型的使用查询的情况下，主动学习可提供最佳建议。 当处于群集状态 5 或更多类似的查询时，每隔 30 分钟，QnA Maker 推荐到知识库设计器以接受或拒绝的基于用户的问题。 所有建议通过相似度聚集在一起，并且根据最终用户的特定查询频率显示替代问题排名靠前的建议。

一旦问题建议 QnA Maker 门户中，需要审阅和接受或拒绝这些建议。 没有一个 API，用于管理的建议。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 隐式反馈的工作原理

QnA Maker 隐式反馈使用算法来确定分数邻近然后对主动学习提出建议。 用于确定置信度的算法并不是一个简单的计算。 在下面的示例范围并不固定的但应使用作为指南以了解算法仅的影响。

当问题的分数置信度很高（例如 80%）时，考虑进行主动学习的分数范围较广，大约在 10% 以内。 随着置信度分数降低（例如 40%），分数范围也会降低，大约在 4% 以内。 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何向提供显式反馈使用训练 API

非常重要，QnA Maker 可以获得有关哪些答案是最佳答案的显式反馈。 如何确定最佳答案取决于您，并且可以包括：

* 选择一个答案的用户反馈。
* 业务逻辑，例如确定可接受评分范围。  
* 这两个用户的反馈和业务逻辑的组合。

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>升级运行时版本使用主动学习

运行时版本 4.4.0 及更高版本支持主动学习。 如果你的知识库是在早期版本上创建的，请[升级运行时](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)以使用此功能。 

## <a name="turn-on-active-learning-to-see-suggestions"></a>打开 active 学习来查看建议

默认情况下，主动学习处于关闭状态。 启用它可以查看建议的问题。 启用主动学习后，您需要将信息从客户端应用程序发送到 QnA Maker。 有关详细信息，请参阅[体系结构流从智能机器人应用程序使用 GenerateAnswer 和训练 Api](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 选择**发布**发布知识库。 主动学习查询从 GenerateAnswer API 预测终结点仅收集。 QnA Maker 门户中测试窗格中的查询不会影响主动学习。

1. 若要打开 active 上 QnA Maker 门户中的学习，请转到右上角，选择你**名称**，请转到[**服务设置**](https://www.qnamaker.ai/UserSettings)。  

    ![打开主动学习建议的问题备选方法，从服务设置页。 在右上方菜单中，选择您的用户名称，然后选择服务设置。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 找到 QnA Maker 服务，然后切换到“主动学习”  。 

    [![在服务设置页中，切换主动学习功能。如果您不能切换功能，您可能需要升级你的服务。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    一次**主动学习**是启用，该知识库定期根据用户提交问题建议新的问题。 可以通过再次切换设置来禁用“主动学习”  。

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>接受在知识库中的主动学习建议

1. 若要查看建议的问题，在**编辑**知识库页上，选择**视图选项**，然后选择**显示主动学习建议**。 

    [![在门户的编辑部分中，选择显示的建议，若要查看主动学习新问题替代项。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 筛选要通过选择显示仅建议的问题和答案对包含的知识库**筛选器由建议**。

    [![使用建议切换筛选器来查看仅主动学习问题的建议替代方法。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 每个 QnA 对建议有一个复选标记，新的问题替代项`✔`，以接受问题或`x`拒绝建议。 选中复选标记可以添加问题。 

    [![选择或通过选择绿色的复选标记或红色删除标记拒绝主动学习问题的建议替代方法。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    可以添加或删除_的所有建议_通过选择**添加所有**或**全部拒绝**上下文的工具栏中。

1. 选择“保存并训练”  ，将所做的更改保存到知识库。

1. 选择**发布**若要允许这些更改可从[GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)。

    当聚集 5 或更多类似的查询时，每隔 30 分钟，QnA Maker 推荐可接受或拒绝的备用问题。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>从智能机器人应用程序使用 GenerateAnswer 和训练 Api 的体系结构流

智能机器人应用程序或其他客户端应用程序应使用以下体系结构的流程来使用主动学习：

* 智能机器人应用程序[从知识库中获取答案](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)GenerateAnswer API，与使用`top`属性来获取答案的次数。
* 智能机器人应用程序确定显式反馈：
    * 使用你自己[自定义业务逻辑](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)，筛选出较低的分数。
    * 在智能机器人应用程序或客户端应用程序中，显示给用户的可能答案的列表并获取用户的选择的答案。
* 智能机器人[发送所选的应答发回 QnA Maker](#bot-framework-sample-code)与[训练 API](#train-api)。


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>在 GenerateAnswer 请求中使用 top 属性以获取多个匹配的答案

提交问题答案，QnA Maker 与时`top`JSON 正文的属性设置要返回的回答数。 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>使用业务逻辑以及评分属性来获取答案，向用户显示的列表

当客户端应用程序 （如聊天机器人） 收到响应时，返回前 3 个问题。 使用`score`属性来分析之间的分数的接近程度。 此邻近范围取决于您自己的业务逻辑。 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>在问题具有相似的分数时的客户应用程序跟进

客户端应用程序显示用户选择的选项问题_单个问题_，大多数表示他们的意图。 

一旦用户选择一个现有问题，客户端应用程序以使用 QnA Maker 训练 API 的反馈方式发送用户的选择。 此反馈完成活动学习反馈循环。 

## <a name="train-api"></a>训练 API

主动学习反馈训练 API POST 请求发送到 QnA Maker。 API 签名是：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 请求属性|Name|Type|目的|
|--|--|--|--|
|URL 路由参数|知识库 ID|字符串|知识库的 GUID。|
|主机子域|QnAMaker 资源名称|字符串|在 Azure 订阅中将 QnA Maker 的主机名。 这是可在设置页上后发布该知识库。 |
|Header|Content-Type|字符串|发送到 API 的正文的媒体类型。 默认值为： `application/json`|
|Header|授权|字符串|终结点密钥 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|POST 正文|JSON 对象|JSON|培训反馈|

JSON 正文具有多个设置：

|JSON 正文属性|Type|目的|
|--|--|--|--|
|`feedbackRecords`|数组|反馈的列表。|
|`userId`|字符串|接受建议的问题的人员的用户 ID。 用户 ID 格式是由您决定。 例如，电子邮件地址可以是您的体系结构中的有效用户 ID。 可选。|
|`userQuestion`|字符串|用户的查询的确切文本。 必需。|
|`qnaID`|数字|在中找到的问题的 ID [GenerateAnswer 响应](metadata-generateanswer-usage.md#generateanswer-response-properties)。 |

一个 JSON 正文示例如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

成功响应将返回 204，而且没有 JSON 响应正文中的状态。 

### <a name="batch-many-feedback-records-into-a-single-call"></a>批处理大量反馈记录到一个调用

在客户端应用程序，如智能机器人应用程序，可以存储数据，然后在中的单个 JSON 正文中发送多条记录`feedbackRecords`数组。 

一个 JSON 正文示例如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>智能机器人应用程序框架的示例代码

Bot framework 代码需要调用训练 API，如果该用户的查询应该用于主动学习。 有两条要编写的代码：

* 确定是否应查询用于主动学习
* 将查询发送回 QnA Maker 训练 API for 主动学习

在中[Azure 智能机器人应用程序示例](https://aka.ms/activelearningsamplebot)，这两种活动具有其进行编程。 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>示例C#代码使用 Bot Framework 训练 API 4.x

下面的代码演示如何将信息发送回 QnA Maker 与训练 API。 这[完整的代码示例](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore)是 GitHub 上提供。

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>使用 Bot Framework 训练 API 的示例 Node.js 代码 4.x 

下面的代码演示如何将信息发送回 QnA Maker 与训练 API。 这[完整的代码示例](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs)是 GitHub 上提供。

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);
            
            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>导出知识库中保存主动学习

当你的应用程序启用，主动学习和导出应用程序中， `SuggestedQuestions` tsv 文件中的列将保留主动学习数据。 

`SuggestedQuestions`列为的隐式的信息的 JSON 对象`autosuggested`，并显式，`usersuggested`反馈。 单个用户提交的问题此 JSON 对象的一个示例`help`是：

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

当你重新导入此应用时，主动学习继续收集的信息，建议您的知识库的建议。 

## <a name="best-practices"></a>最佳做法

有关使用主动学习的最佳做法，请参阅[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="next-steps"></a>后续步骤
 
> [!div class="nextstepaction"]
> [将用于 GenerateAnswer API 元数据](metadata-generateanswer-usage.md)
