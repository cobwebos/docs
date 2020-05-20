---
title: 改进知识库 - QnA Maker
description: 通过主动学习提高知识库的质量。 查看、接受或拒绝，添加时不删除或更改现有问题。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 2e074716e4342a8748de4fb4e217548f1cb731f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650773"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>接受知识库中的活动学习建议问题


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

活动学习在批准建议后将更改知识库或搜索服务，并保存和训练。 如果你批准此建议，则会将其添加为替代问题。

## <a name="turn-on-active-learning"></a>启用主动学习

若要查看建议的问题，必须打开 QnA Maker 资源的[活动学习](use-active-learning.md)。

## <a name="view-suggested-questions"></a>查看建议的问题

1. 若要查看建议的问题，请在 "**编辑**知识库" 页上选择 "**查看选项**"，然后选择 "**显示活动的学习建议**"。

    [![在门户的 "编辑" 部分中，选择 "显示建议"，以查看活动学习的新问题备选方案。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 通过选择 "**按建议筛选**" 来使用问题和答案对筛选知识库，以便仅显示建议。

    [![使用 "按建议筛选" 切换来仅查看活动学习的建议问题备选方案。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 每个 QnA 对用复选标记建议新的问题替代项， `✔` 以接受问题或 `x` 拒绝建议。 选中复选标记可以添加问题。

    [![选择或拒绝活动学习的建议问题，方法是选择绿色复选标记或红色删除标记。](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以通过在上下文工具栏中选择 "**全部添加**" 或 "**全部拒绝**" 来添加或删除_所有建议_。

1. 选择“保存并训练”****，将所做的更改保存到知识库。

1. 选择 "**发布**" 以允许在[GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)中提供更改。

    如果有5个或更多个类似查询被聚集，每30分钟 QnA Maker 建议要接受或拒绝的替代问题。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>使用 GenerateAnswer 并通过 bot 训练 Api 的体系结构流

机器人或其他客户端应用程序应使用以下体系结构流来使用活动学习：

* 机器人使用 GenerateAnswer API[从知识库获取答案](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)，并使用 `top` 属性获取多个答案。
* 机器人确定显式反馈：
    * 使用您自己的[自定义业务逻辑](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)，筛选出低分数。
    * 在机器人或客户端应用程序中，显示用户的可能答案列表，并获得用户选定的答案。
* 机器人会[将选择的应答发送回 QnA Maker](#bot-framework-sample-code)的[训练 API](#train-api)。


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>使用 GenerateAnswer 请求中的 top 属性获取几个匹配的答案

提交问题以 QnA Maker 获取答案时， `top` JSON 正文的属性将设置要返回的应答的数目。

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>使用评分属性以及业务逻辑获取显示用户的答案列表

当客户端应用程序（如聊天机器人）收到响应时，将返回前3个问题。 使用 `score` 属性可分析分数之间的邻近性。 此邻近范围取决于你自己的业务逻辑。

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

客户端应用程序显示问题，其中包含一个选项，让用户选择最能表示其意图的_单个问题_。

一旦用户选择了一个现有问题，客户端应用程序就会使用 QnA Maker 的训练 API 作为反馈发送用户的选择。 此反馈将完成活动的学习反馈循环。

## <a name="train-api"></a>训练 API

向训练 API POST 请求发送活动学习反馈 QnA Maker。 API 签名如下：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 请求属性|名称|类型|目标|
|--|--|--|--|
|URL 路由参数|知识库 ID|字符串|知识库的 GUID。|
|自定义子域|QnAMaker 资源名称|字符串|资源名称用作 QnA Maker 的自定义子域。 发布知识库后，可以在 "设置" 页上找到此功能。 它作为列出 `host` 。|
|标头|Content-Type|字符串|发送到 API 的正文的媒体类型。 默认值为：`application/json`|
|标头|Authorization|字符串|终结点密钥 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|POST 正文|JSON 对象|JSON|培训反馈|

JSON 正文具有几个设置：

|JSON 正文属性|类型|目标|
|--|--|--|--|
|`feedbackRecords`|array|反馈列表。|
|`userId`|字符串|接受建议问题的人员的用户 ID。 用户 ID 格式由您来了解。 例如，电子邮件地址可以是体系结构中的有效用户 ID。 可选。|
|`userQuestion`|字符串|用户查询的确切文本。 必需。|
|`qnaID`|number|[GenerateAnswer 响应](metadata-generateanswer-usage.md#generateanswer-response-properties)中找到的问题 ID。 |

示例 JSON 正文如下所示：

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

成功的响应将返回状态204，并且不会返回 JSON 响应正文。

### <a name="batch-many-feedback-records-into-a-single-call"></a>成批发送多条反馈记录到一个调用中

在客户端应用程序（如机器人）中，可以存储数据，然后在数组中的一个 JSON 正文中发送多个记录 `feedbackRecords` 。

示例 JSON 正文如下所示：

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

如果用户的查询应该用于活动学习，则机器人框架代码需要调用训练 API。 需要编写两个代码片段：

* 确定是否应将查询用于主动学习
* 将查询发送回用于活动学习 QnA Maker 的训练 API

在[Azure 机器人示例](https://aka.ms/activelearningsamplebot)中，这两个活动都已进行了编程。

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>用 Bot Framework 4.x 训练 API 的示例 c # 代码

下面的代码演示如何将信息与训练 API 一起发送回 QnA Maker。

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>用于为 Bot Framework 4.x 定型 API 的示例 Node.js 代码

下面的代码演示如何将信息与训练 API 一起发送回 QnA Maker。

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>活动学习保存在导出的知识库中

当你的应用启用了活动学习并导出应用后， `SuggestedQuestions` tsv 文件中的列将保留活动的学习数据。

此 `SuggestedQuestions` 列是隐式、和显式反馈信息的 JSON 对象 `autosuggested` `usersuggested` 。 此 JSON 对象的一个示例是，针对的单个用户提交的问题 `help` 是：

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

当您重新导入此应用程序时，主动学习将继续收集相关信息并为您的知识库提出建议。



## <a name="best-practices"></a>最佳实践

有关使用主动学习的最佳做法，请参阅[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将元数据与 GenerateAnswer API 配合使用](metadata-generateanswer-usage.md)
