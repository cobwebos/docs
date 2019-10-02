---
title: LUIS 和 QnAMaker - 机器人集成
titleSuffix: Azure Cognitive Services
description: 随着 QnA Maker 知识库变得越来越大，将其作为单个整体集进行维护变得困难，并且需要将知识库分成更小的逻辑块。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e1ea234bde96ce84259841bbc592bf6373bc639
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802799"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>将机器人与 QnA Maker 和 LUIS 结合使用以分发知识库
随着 QnA Maker 知识库变得越来越大，将其作为单个整体集进行维护变得困难，并且需要将知识库分成更小的逻辑块。

虽然在 QnA Maker 中创建多个知识库很简单，但你需要一些逻辑来将传入的问题路由到适当的知识库。 可以使用 LUIS 来完成此操作。

本文使用 Bot Framework v3 SDK。 如果你对此信息的 Bot Framework v4 SDK 版本感兴趣，请参阅此 [Bot Framework 文章](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)。

## <a name="architecture"></a>体系结构

![语言理解体系结构的 QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

在上面的方案中，QnA Maker 首先从 LUIS 模型获取传入问题的意向，然后使用该意向将问题路由到正确的 QnA Maker 知识库。

## <a name="create-a-luis-app"></a>创建 LUIS 应用

1. 登录到 [LUIS](https://www.luis.ai/) 门户。
1. [创建应用](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)。
1. 为每个 QnA Maker 知识库[添加意向](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents)。 示例陈述应对应于 QnA Maker 知识库中的问题。
1. [训练 LUIS 应用](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)和[发布 LUIS 应用](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)。
1. 在 "**管理**" 部分中，记下 "LUIS" 应用 ID、"LUIS 终结点密钥" 和 "[自定义域名](../../cognitive-services-custom-subdomains.md)"。 在后面的步骤中会用到这些值。 

## <a name="create-qna-maker-knowledge-bases"></a>创建 QnA Maker 知识库

1. 登录到 [QnA Maker](https://qnamaker.ai)。
1. 为 LUIS 应用中的每个意向[创建](https://www.qnamaker.ai/Create)知识库。
1. 测试并发布知识库。 发布每个 KB 时，记下 "KB ID"、"资源名称" （ _azurewebsites.net/qnamaker 的_自定义子域）和 "授权终结点" 密钥。 在后面的步骤中会用到这些值。 

    本文假定所有知识库都是在同一 Azure QnA Maker 订阅中创建的。

    ![QnA Maker HTTP 请求](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web 应用机器人

1. 创建自动包含 LUIS 应用的["基本" Web 应用机器人](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)。 选择C#编程语言。

1. 创建 Web 应用机器人后，在 Azure 门户中，选择 Web 应用机器人。
1. 选择 Web 应用机器人服务导航中的“应用程序设置”，然后向下滚动到可用设置的“应用程序设置”部分。
1. 将“LuisAppId”更改为上一部分中创建的 LUIS 应用的值，然后选择“保存”。


## <a name="change-code-in-basicluisdialogcs"></a>更改 BasicLuisDialog.cs 中的代码
1. 在 Azure 门户上的 Web 应用机器人导航的“机器人管理”部分，选择“生成”。
2. 选择“打开联机代码编辑器”。 在联机编辑环境中打开新的浏览器选项卡。 
3. 在“WWWROOT”部分，选择“对话框”目录，然后打开“BasicLuisDialog.cs”。
4. 将依赖项添加到“BasicLuisDialog.cs”文件的顶部：

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. 添加以下类，以反序列化 QnA Maker 响应：

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. 添加以下类，以向 QnA Maker 服务发出 HTTP 请求。 请注意，授权标头的值包含单词 `EndpointKey`，该单词后跟一个空格。 JSON 结果被反序列化为上述类，并返回第一个答案。

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. 修改 BasicLuisDialog 类。 每个 LUIS 意向都应具有一个使用 LuisIntent 修饰的方法。 修饰的参数是实际的 LUIS 意向名称。 被修饰的方法名称应为具备可读性和可维护性的 LUIS 意向名称，但不必与在设计或运行时使用的名称相同。  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>生成机器人
1. 在代码编辑器中，右键单击 `build.cmd`，并选择“从控制台运行”。

    ![从控制台运行](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 代码视图将替换为显示生成进度和结果的终端窗口。

    ![控制台生成](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>测试机器人
在 Azure 门户中，选择“在 Web 聊天中测试”以测试机器人。 键入出于不同意向的消息以从相应的知识库获得响应。

![Web 聊天测试](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为 QnA Maker 创建业务连续性计划](../How-To/business-continuity-plan.md)
