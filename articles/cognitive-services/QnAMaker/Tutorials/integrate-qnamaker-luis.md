---
title: LUIS 和 QnAMaker - 机器人集成
titleSuffix: Azure Cognitive Services
description: 随着 QnA Maker 知识库的扩大，很难将其作为单一集进行维护。 将知识库拆分为更小的逻辑区块。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402718"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>使用带有 QnA 制造商和 LUIS 的机器人来分发您的知识库
随着 QnA Maker 知识库的扩大，很难将其作为单一集进行维护。 将知识库拆分为更小的逻辑区块。

尽管在 QnA Maker 中创建多个知识库非常简单，但您需要一些逻辑才能将传入的问题路由到相应的知识库。 可以使用 LUIS 来完成此操作。

本文使用机器人框架 v3 SDK。 如果您对此信息的 Bot Framework v4 SDK 版本感兴趣，请参阅[使用多个 LUIS 和 QnA 模型](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)。

## <a name="architecture"></a>体系结构

![显示具有语言理解的 QnA 制造商架构的图形](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

上图显示 QnA Maker 首先从 LUIS 模型获取传入问题的意图。 然后，QnA Maker 使用该意图将问题路由到正确的 QnA Maker 知识库。

## <a name="create-a-luis-app"></a>创建 LUIS 应用

1. 登录到 [LUIS](https://www.luis.ai/) 门户。
1. [创建应用](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app)。
1. 为每个 QnA Maker 知识库[添加意向](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents)。 示例陈述应对应于 QnA Maker 知识库中的问题。
1. [训练 LUIS 应用程序](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)并[发布 LUIS 应用程序](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)。
1. 在 **"管理"** 部分中，记下 LUIS 应用 ID、LUIS 终结点密钥和[自定义域名](../../cognitive-services-custom-subdomains.md)。 在后面的步骤中会用到这些值。

## <a name="create-qna-maker-knowledge-bases"></a>创建 QnA Maker 知识库

1. 登录到[QnA 制造商](https://qnamaker.ai)。
1. 为 LUIS 应用中的每个意图[创建](https://www.qnamaker.ai/Create)知识库。
1. 测试并发布知识库。 发布每个子域时，请注意 ID、资源名称 _（.azurewebsites.net/qnamaker_之前的自定义子域）和授权终结点密钥。 在后面的步骤中会用到这些值。

    本文假定知识库都在同一 Azure QnA Maker 订阅中创建。

    ![QnA 制造商 HTTP 请求的屏幕截图](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web 应用机器人

1. [使用 Azure 自动程序服务创建"基本"机器人](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)，该自动包含 LUIS 应用。 选择 C# 编程语言。

1. 创建 Web 应用自动程序后，在 Azure 门户中选择 Web 应用自动程序。
1. 在 Web 应用自动程序服务导航中，选择 **"应用程序设置**"。 然后向下滚动到可用**设置的应用程序设置**部分。
1. 将**LuisAppId**更改为上一节中创建的 LUIS 应用的值。 再选择“保存”****。


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>更改BasicLuisDialog.cs文件中的代码
1. 在 Azure 门户上的 Web 应用机器人导航的“机器人管理”**** 部分，选择“生成”****。
2. 选择“打开联机代码编辑器”****。 在联机编辑环境中打开新的浏览器选项卡。
3. 在**WWWROOT**部分中，选择 **"对话框"** 目录，然后打开**BasicLuisDialog.cs**。
4. 将依赖项添加到“BasicLuisDialog.cs”**** 文件的顶部：

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

5. 添加以下类以取消序列化 QnA Maker 响应：

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


6. 添加以下类，以向 QnA Maker 服务发出 HTTP 请求。 请注意，**授权**标头的值包括单词 ，`EndpointKey`单词后有一个空格。 JSON 结果被反序列化到前面的类中，并返回第一个答案。

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


7. 修改类`BasicLuisDialog`。 每个 LUIS 意向都应具有一个使用 LuisIntent**** 修饰的方法。 修饰的参数是实际的 LUIS 意向名称。 修饰的方法名称_应_为可读性和可维护性的 LUIS 意图名称，但在设计或运行时不必相同。

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
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
1. 在代码编辑器中，右键单击**build.cmd，** 然后选择"**从控制台运行**"。

    ![代码编辑器中"从控制台运行"选项的屏幕截图](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 代码视图替换为显示生成进度和结果的终端窗口。

    ![控制台生成屏幕截图](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>测试机器人
在 Azure 门户中，选择“在 Web 聊天中测试”**** 以测试机器人。 键入出于不同意向的消息以从相应的知识库获得响应。

![网络聊天测试的屏幕截图](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将您的知识库与 Power 虚拟代理中的代理集成](integrate-with-power-virtual-assistant-fallback-topic.md)
