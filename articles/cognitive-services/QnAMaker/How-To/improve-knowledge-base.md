---
title: 改进知识库 - QnA Maker
description: 通过积极学习提高知识库的质量。 审核、接受或拒绝，添加而不删除或更改现有问题。
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1eb0ed42f700c14350a5e4f1eff9b7592cbf8ef6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474881"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>在知识库中接受主动学习建议的问题


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

"活动学习"在批准建议后更改知识库或搜索服务，然后保存并训练。 如果您批准该建议，它将作为备用问题添加。

## <a name="turn-on-active-learning"></a>启用主动学习

要查看建议的问题，您必须为 QnA Maker 资源[打开主动学习](use-active-learning.md)。

## <a name="view-suggested-questions"></a>查看建议的问题

1. 要查看建议的问题，在 **"编辑**知识库"页上，选择 **"查看选项**"，然后选择 **"显示活动学习建议**"。

    [![在门户的"编辑"部分，选择"显示建议"以查看活动学习的新问题备选方案。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 使用问答对筛选知识库，通过选择 **"按建议筛选"** 来仅显示建议。

    [![使用"按建议筛选"切换仅查看活动学习建议的问题备选方案。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 每个 QnA 对都建议使用复选标记的新问题备选方案`✔`，以接受问题或拒绝`x`建议。 选中复选标记可以添加问题。

    [![通过选择绿色复选标记或红色删除标记，选择或拒绝活动学习建议的问题备选方案。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以通过选择 **"添加所有**"或 **"拒绝**上下文工具栏中的所有建议"来添加或删除_所有建议_。

1. 选择“保存并训练”****，将所做的更改保存到知识库。

1. 选择 **"发布"** 以允许从[生成应答 API](metadata-generateanswer-usage.md#generateanswer-request-configuration)中提供更改。

    当 5 个或更多类似的查询被群集时，每 30 分钟，QnA Maker 会建议您接受或拒绝其他问题。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>用于使用来自自动程序的生成应答和训练 API 的体系结构流

自动程序或其他客户端应用程序应使用以下体系结构流来使用活动学习：

* Bot 使用生成应答 API[从知识库中获取答案](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)，使用`top`属性获取多个答案。
* 机器人确定显式反馈：
    * 使用您自己的[自定义业务逻辑](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)，过滤掉低分数。
    * 在自动程序或客户端应用程序中，显示用户可能的答案列表，并获取用户选择的答案。
* 机器人使用[训练 API](#train-api)[将选定的答案发送回 QnA 制造商](#bot-framework-sample-code)。


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>使用"生成应答"请求中的顶部属性获取多个匹配的答案

向 QnA Maker 提交问题以进行回答时，JSON`top`正文的属性将设置要返回的答案数。

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>使用分数属性以及业务逻辑获取答案列表以显示用户

当客户端应用程序（如聊天机器人）收到响应时，将返回前 3 个问题。 使用`score`属性分析分数之间的邻近程度。 此邻近范围由您自己的业务逻辑决定。

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
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

客户端应用程序显示问题，并允许用户选择最代表其意图_的单一问题_。

用户选择现有问题之一后，客户端应用程序将使用 QnA Maker 的火车 API 将用户的选择作为反馈发送。 此反馈完成活动学习反馈循环。

## <a name="train-api"></a>训练 API

主动学习反馈通过培训 API POST 请求发送给 QnA 制造商。 API 签名是：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 请求属性|名称|类型|目的|
|--|--|--|--|
|URL 路由参数|知识库 ID|字符串|知识库的 GUID。|
|自定义子域|QnAMaker 资源名称|字符串|资源名称用作 QnA Maker 的自定义子域。 在发布知识库后，"设置"页上提供了此功能。 它被列为 。 `host`|
|标头|Content-Type|字符串|发送到 API 的正文的媒体类型。 默认值为：`application/json`|
|标头|授权|字符串|终结点密钥 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|POST 正文|JSON 对象|JSON|培训反馈|

JSON 正文具有多个设置：

|JSON 正文属性|类型|目的|
|--|--|--|--|
|`feedbackRecords`|array|反馈列表。|
|`userId`|字符串|接受建议问题的人员的用户 ID。 用户 ID 格式由您决定。 例如，电子邮件地址可以是体系结构中的有效用户 ID。 可选。|
|`userQuestion`|字符串|用户查询的确切文本。 必需。|
|`qnaID`|数字|问题的 ID，在[生成答案响应](metadata-generateanswer-usage.md#generateanswer-response-properties)中找到。 |

JSON 正文的示例如下所示：

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

成功响应返回状态为 204，没有 JSON 响应机构。

### <a name="batch-many-feedback-records-into-a-single-call"></a>将许多反馈记录批处理为单个呼叫

在客户端应用程序中（如自动程序），可以存储数据，然后在`feedbackRecords`数组中的单个 JSON 正文中发送许多记录。

JSON 正文的示例如下所示：

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

## <a name="bot-framework-sample-code"></a>机器人框架示例代码

如果用户的查询应用于活动学习，则自动程序框架代码需要调用训练 API。 有两段代码要编写：

* 确定是否应将查询用于活动学习
* 将查询发送回 QnA 制造商的列车 API 以进行主动学习

在[Azure 机器人示例中](https://aka.ms/activelearningsamplebot)，这两个活动都已编程。

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>使用机器人框架 4.x 训练 API 的示例 C# 代码

以下代码说明了如何使用训练 API 将信息发送回 QnA 制造商。

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
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>带有 Bot 框架 4.x 的火车 API 的示例 Node.js 代码

以下代码说明了如何使用训练 API 将信息发送回 QnA 制造商。

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>主动学习保存在导出的知识库中

当应用启用了活动学习并导出应用时，tsv`SuggestedQuestions`文件中的列将保留活动学习数据。

该`SuggestedQuestions`列是隐式、`autosuggested`显式反馈和显式`usersuggested`反馈信息的 JSON 对象。 对于单个用户提交的问题，此 JSON 对象的示例`help`是：

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

您还可以使用下载更改 API 使用 REST 或任何基于语言的 SDK 来查看这些更改：
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


当您重新导入此应用程序时，活动学习将继续收集信息并为您的知识库提供建议。



## <a name="best-practices"></a>最佳做法

有关使用主动学习的最佳做法，请参阅[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将元数据与 GenerateAnswer API 配合使用](metadata-generateanswer-usage.md)
